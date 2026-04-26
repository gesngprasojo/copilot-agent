---
mode: ask
description: Generate unit test lengkap untuk kode yang dipilih
---

Buatkan unit test yang komprehensif untuk kode berikut:

**Aturan:**
- Gunakan framework testing yang sesuai dengan bahasa (pytest, jest, junit, dll)
- Ikuti pola AAA: Arrange, Act, Assert
- Cover semua branch: happy path, edge case, error case
- Gunakan mock/stub untuk dependency eksternal
- Nama test harus deskriptif: `test_should_[expected]_when_[condition]`

**Wajib cover:**
- [ ] Happy path (input valid)
- [ ] Input kosong / null
- [ ] Input boundary (min/max)
- [ ] Error handling
- [ ] Side effects (jika ada)

${selection}
