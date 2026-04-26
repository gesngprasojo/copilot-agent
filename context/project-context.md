# Project Context — OrderEase

> File ini memberikan konteks domain kepada AI agar respons lebih relevan.
> Contoh di bawah menggunakan domain SaaS manajemen pesanan UMKM (OrderEase).
> **Ganti** konten ini sesuai project Anda, pertahankan strukturnya.

---

## Domain Bisnis

**Industri**: SaaS — Manajemen Pesanan B2C untuk UMKM

**Problem yang diselesaikan**:
UMKM (toko online, reseller) kesulitan mengelola pesanan dari banyak channel (Tokopedia, WhatsApp, Instagram) secara bersamaan. OrderEase menyatukan semua pesanan ke satu dashboard, otomasi update stok, dan generate laporan penjualan harian.

**User utama**:

- `Pemilik Toko` — melihat dashboard ringkasan penjualan, menarik laporan, mengatur harga & promo
- `Staff Gudang` — mengkonfirmasi ketersediaan stok, update status pengiriman
- `Customer` — melihat status pesanan via tracking link (read-only, tanpa login)

---

## Terminologi Domain (Ubiquitous Language)

> Gunakan istilah-istilah ini secara konsisten di kode, dokumentasi, dan komunikasi.

| Istilah            | Definisi                                                                                       |
| ------------------ | ---------------------------------------------------------------------------------------------- |
| `Order`            | Pesanan yang masuk dari channel manapun. Punya lifecycle: PENDING → CONFIRMED → SHIPPED → DONE |
| `OrderItem`        | Satu baris produk dalam sebuah Order (product_id, qty, unit_price saat order dibuat)           |
| `Channel`          | Sumber pesanan masuk: `TOKOPEDIA`, `WHATSAPP`, `INSTAGRAM`, `DIRECT`                           |
| `Fulfillment`      | Proses pengiriman fisik dari gudang ke customer (bisa pakai kurir berbeda per order)           |
| `SKU`              | Kode unik produk di gudang. Berbeda dengan product_id di channel marketplace                   |
| `StockReservation` | Stok yang "dikunci" untuk order CONFIRMED tapi belum dikirim                                   |
| `Voucher`          | Kode diskon yang bisa dipakai sekali atau multi-pakai, dengan batas waktu & min. belanja       |

---

## Aturan Bisnis Kritis

1. **Stok tidak boleh negatif** — Saat order dikonfirmasi, sistem harus `reserve` stok terlebih dahulu. Jika stok tidak cukup, order harus masuk status `WAITLISTED`, bukan langsung ditolak.
2. **Harga di `OrderItem` adalah harga saat pesanan dibuat** — Perubahan harga produk tidak boleh mengubah harga pesanan yang sudah ada.
3. **Voucher berlaku sekali per customer** — Kecuali voucher bertipe `MULTI_USE`, validasi harus cek `voucher_usages` tabel sebelum apply diskon.
4. **Diskon maksimal 80% dari harga normal** — Hardcoded di `DiscountPolicy`, tidak bisa di-override dari UI.
5. **Order tidak bisa dihapus** — Hanya bisa di-cancel. Data tetap ada untuk audit trail.
6. **Channel WHATSAPP** memerlukan konfirmasi manual oleh staff sebelum masuk CONFIRMED.

---

## Integrasi Eksternal

| Sistem        | Tujuan                                | Protokol       | Environment Variable                         |
| ------------- | ------------------------------------- | -------------- | -------------------------------------------- |
| Tokopedia API | Sync pesanan & update status otomatis | REST           | `TOKOPEDIA_APP_ID`, `TOKOPEDIA_APP_SECRET`   |
| Shipper.id    | Hitung ongkir, booking kurir          | REST           | `SHIPPER_API_KEY`                            |
| Midtrans      | Payment gateway untuk channel DIRECT  | REST + Webhook | `MIDTRANS_SERVER_KEY`, `MIDTRANS_CLIENT_KEY` |
| Firebase FCM  | Push notifikasi ke mobile app staff   | SDK            | `FIREBASE_SERVICE_ACCOUNT_JSON`              |
| Sentry        | Error tracking & performance monitor  | SDK            | `SENTRY_DSN`                                 |

---

## Hal yang TIDAK boleh dilakukan AI

- **Jangan auto-generate migrasi Alembic** — selalu review dulu karena ada tabel legacy `order_legacy` yang masih dipakai oleh sistem lama
- **Jangan ubah skema tabel `voucher_usages`** — dipakai oleh billing service eksternal yang belum bisa diupdate
- **Jangan hapus field `external_order_id`** di tabel `orders` — dipakai untuk reconciliation dengan Tokopedia
- **Jangan generate kode yang langsung deduct stok** tanpa melalui `StockService.reserve()` — bypass policy ini menyebabkan stok negatif
- **Jangan expose harga cost (HPP)** di API response publik — hanya boleh di endpoint internal `/internal/`
