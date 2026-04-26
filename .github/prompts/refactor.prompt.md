---
agent: ask
description: Refactor kode agar lebih bersih dan maintainable
---

Refactor kode berikut dengan mempertahankan fungsionalitas yang sama:

**Tujuan refactor:**

1. Terapkan prinsip SOLID (terutama SRP dan OCP)
2. Hilangkan duplikasi (DRY)
3. Perbaiki penamaan variabel/fungsi agar lebih ekspresif
4. Pecah fungsi panjang (> 20 baris) menjadi lebih kecil
5. Tambahkan type hint / type annotation
6. Hapus komentar yang hanya menjelaskan "apa" bukan "mengapa"

**Constraints:**

- Jangan ubah public API / signature fungsi yang sudah ada
- Jangan tambah fitur baru
- Pastikan semua test tetap lulus

${selection}
