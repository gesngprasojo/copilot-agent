# Memory — Lessons Learned — OrderEase

> Catatan pelajaran dari project ini yang relevan untuk AI di session berikutnya.
> Update setelah menyelesaikan task penting atau menemukan gotcha.
> Format: `[YYYY-MM-DD] — [pelajaran]`

---

## Keputusan Teknis

- **2024-03-15** — Memilih `selectinload` bukan `joinedload` untuk relasi `Order.items` karena joinedload menghasilkan duplikasi row saat ada banyak items. Trade-off: 2 query terpisah, tapi data bersih.
- **2024-04-02** — Pindah dari `datetime.now()` ke `datetime.utcnow()` di semua model. Timestamp di DB harus selalu UTC, konversi ke timezone lokal hanya di frontend.
- **2024-04-20** — Gunakan `Decimal` bukan `float` untuk semua kalkulasi harga. `float` menyebabkan rounding error di total invoice (misal: 99999.99999999 jadi 100000.0).

---

## Gotcha & Pitfall

- **SQLAlchemy Async**: Jangan akses lazy relationship di luar session context. Selalu gunakan `selectinload` atau `joinedload` di query awal, atau akan dapat error `MissingGreenlet`.

  ```python
  # ❌ Error: akses order.items setelah session closed
  order = await order_repo.get_by_id(db, order_id)
  # session closes here...
  print(order.items)  # MissingGreenlet error!

  # ✅ Load sekaligus
  order = await order_repo.get_with_items(db, order_id)  # pakai selectinload
  ```

- **Alembic + PostgreSQL UUID**: Kolom UUID di PostgreSQL perlu `server_default=text("gen_random_uuid()")`, bukan `default=uuid4`. Yang Python-side default tidak dijalankan saat insert via raw SQL atau migration seed.

- **Redis Stock Lock**: TTL kunci Redis harus lebih panjang dari estimasi waktu proses order. Kami set 5 menit, tapi pernah timeout saat DB slow → order gagal konfirmasi padahal stok tersedia. Naikkan ke 10 menit.

- **Tokopedia Webhook**: Webhook Tokopedia kadang kirim event duplikat dalam 30 detik. Selalu implementasi idempotency check dengan `external_order_id` sebelum insert order baru.

  ```python
  # ✅ Idempotency check
  existing = await order_repo.get_by_external_id(db, webhook_data.order_id)
  if existing:
      return existing  # sudah diproses, return existing order
  ```

- **Pydantic v2 breaking change**: Jika upgrade dari Pydantic v1 ke v2, `orm_mode = True` berubah jadi `model_config = ConfigDict(from_attributes=True)`. Jangan lupa update semua schema.

---

## Pattern yang Berhasil

- **Repository Pattern** bekerja sangat baik untuk isolasi test. Dengan mock `OrderRepository`, kita bisa test `OrderService` tanpa butuh koneksi DB nyata.
- **FastAPI Background Tasks** untuk kirim notifikasi setelah order confirmed. User tidak perlu tunggu notifikasi sebelum dapat response 201.
- **Pydantic `validator` dengan `each_item=True`** untuk validasi list items dalam satu request — lebih clean dari loop manual di service layer.

---

## Pattern yang TIDAK Berhasil

- **Celery untuk stock reservation** — terlalu complex, ada race condition antar worker. Diganti dengan Redis atomic operations langsung.
- **Soft delete dengan `is_deleted` flag** di tabel `orders` — menyulitkan query (harus selalu append `WHERE is_deleted = false`). Sekarang pakai `cancelled_at` timestamp sebagai penanda order dibatalkan.

---

## Perintah Penting

```bash
# Setup project pertama kali
cp .env.example .env
docker-compose up -d db redis
pip install -r requirements.txt
alembic upgrade head
uvicorn app.main:app --reload

# Jalankan semua test
pytest tests/ -v --cov=app --cov-report=term-missing

# Jalankan test satu module saja
pytest tests/test_orders/ -v

# Buat migrasi baru (setelah update model)
alembic revision --autogenerate -m "add voucher_usages table"
# ⚠️ Selalu review file migrasi yang dihasilkan sebelum apply!

# Apply migrasi
alembic upgrade head

# Rollback migrasi terakhir
alembic downgrade -1

# Cek status migrasi
alembic current
```
