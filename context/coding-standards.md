# Coding Standards

> Panduan ini dibaca oleh AI untuk menghasilkan kode yang konsisten.
> Update sesuai konvensi project Anda.

## Penamaan (Naming Convention)

| Konteks           | Gaya          | Contoh                      |
|-------------------|---------------|-----------------------------|
| Variabel / fungsi | camelCase      | `getUserById`               |
| Kelas             | PascalCase     | `UserRepository`            |
| Konstanta         | UPPER_SNAKE    | `MAX_RETRY_COUNT`           |
| File (Python)     | snake_case     | `user_service.py`           |
| File (JS/TS)      | kebab-case     | `user-service.ts`           |
| Database tabel    | snake_case     | `user_profiles`             |
| Env variable      | UPPER_SNAKE    | `DATABASE_URL`              |

## Struktur Fungsi

```python
# ✅ Good — fungsi kecil, satu tanggung jawab
def calculate_discount(price: float, rate: float) -> float:
    """Hitung diskon dari harga dan rate."""
    if rate < 0 or rate > 1:
        raise ValueError(f"Rate harus antara 0 dan 1, dapat: {rate}")
    return price * rate

# ❌ Bad — terlalu banyak tanggung jawab
def process_order(order):
    # validate, calculate, save, send email — semua dalam satu fungsi
    ...
```

## Error Handling

- **Gunakan custom exception** untuk domain error yang spesifik
- **Jangan silent catch** (`except: pass`) — selalu log atau re-raise
- **Validasi di boundary** — API endpoint, CLI input, event consumer
- **Jangan validasi ulang** di dalam domain logic jika sudah divalidasi di boundary

```python
# ✅ Good
class InsufficientStockError(Exception):
    def __init__(self, product_id: str, requested: int, available: int):
        self.product_id = product_id
        super().__init__(f"Stok tidak cukup untuk {product_id}: minta {requested}, ada {available}")
```

## Keamanan

- **Tidak ada hardcoded secret** — gunakan environment variable
- **Parameterized query** — jangan string concatenation untuk SQL
- **Sanitize output** — escape HTML/JSON sebelum render ke client
- **Rate limiting** — terapkan di semua public endpoint

## Testing

- Satu file test per module: `test_[nama_module].py`
- Nama test: `test_should_[expected]_when_[condition]`
- Coverage minimum: **80%** untuk logic bisnis
- Gunakan fixture/factory untuk data test, bukan hardcoded value

## Commit Message

Format: `[type]: [deskripsi singkat]`

| Type       | Kapan digunakan                          |
|------------|------------------------------------------|
| `feat`     | Fitur baru                               |
| `fix`      | Bug fix                                  |
| `refactor` | Refactor tanpa perubahan fungsionalitas  |
| `test`     | Tambah / update test                     |
| `docs`     | Update dokumentasi                       |
| `chore`    | Setup, config, dependency update         |

Contoh: `feat: tambah endpoint export laporan ke CSV`
