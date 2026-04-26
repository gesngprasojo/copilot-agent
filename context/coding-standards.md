# Coding Standards — OrderEase

> Panduan ini dibaca oleh AI untuk menghasilkan kode yang konsisten.
> Semua contoh menggunakan domain OrderEase (Python/FastAPI + TypeScript/React).

---

## Penamaan (Naming Convention)

| Konteks               | Gaya                | Contoh OrderEase                               |
| --------------------- | ------------------- | ---------------------------------------------- |
| Variabel / fungsi     | snake_case          | `get_order_by_id`, `reserved_qty`              |
| Kelas                 | PascalCase          | `OrderService`, `StockReservation`             |
| Konstanta             | UPPER_SNAKE         | `MAX_DISCOUNT_RATE`, `VOUCHER_EXPIRY_DAYS`     |
| File Python           | snake_case          | `order_service.py`, `stock_repository.py`      |
| File TypeScript/React | kebab-case          | `order-detail.tsx`, `use-order-status.ts`      |
| Database tabel        | snake_case          | `orders`, `order_items`, `voucher_usages`      |
| Env variable          | UPPER_SNAKE         | `DATABASE_URL`, `REDIS_URL`, `SHIPPER_API_KEY` |
| Pydantic schema       | PascalCase + suffix | `CreateOrderRequest`, `OrderResponse`          |
| FastAPI router prefix | kebab-case          | `/orders`, `/order-items`, `/vouchers`         |

---

## Struktur Layer (Wajib Diikuti)

```
Router  →  Service  →  Repository  →  Database
  ↑            ↑
Pydantic    Domain Exception
Schema
```

**Aturan antar layer:**

- `router.py` → hanya routing + dependency injection, **tidak ada** business logic
- `service.py` → semua business logic, **tidak ada** query SQL langsung
- `repository.py` → semua query DB, **tidak ada** business logic

---

## Struktur Fungsi

```python
# ✅ Good — satu tanggung jawab, type hint lengkap, exception spesifik
async def confirm_order(
    order_id: UUID,
    confirmed_by: UUID,
    db: AsyncSession,
) -> Order:
    order = await order_repo.get_by_id(db, order_id)
    if order is None:
        raise OrderNotFoundError(order_id)
    if order.status != OrderStatus.PENDING:
        raise InvalidOrderTransitionError(order.status, OrderStatus.CONFIRMED)

    await stock_service.reserve_bulk(db, order.items)
    order.status = OrderStatus.CONFIRMED
    order.confirmed_by = confirmed_by
    order.confirmed_at = datetime.utcnow()
    return order


# ❌ Bad — banyak tanggung jawab, tidak ada type hint, silent catch
def process_order(order_id):
    try:
        order = db.query(f"SELECT * FROM orders WHERE id = '{order_id}'")  # SQL injection!
        # validasi, update stok, kirim email, update status — semua di sini
        db.execute(f"UPDATE orders SET status = 'CONFIRMED' WHERE id = '{order_id}'")
    except:
        pass  # ← silent catch, bug tidak akan terdeteksi
```

---

## Error Handling

```python
# ✅ Good — custom exception per domain, detail yang cukup untuk debug
class InsufficientStockError(Exception):
    def __init__(self, sku: str, requested: int, available: int):
        self.sku = sku
        self.requested = requested
        self.available = available
        super().__init__(
            f"Stok tidak cukup untuk SKU '{sku}': diminta {requested}, tersedia {available}"
        )

class InvalidOrderTransitionError(Exception):
    def __init__(self, current: OrderStatus, target: OrderStatus):
        super().__init__(
            f"Tidak bisa transisi order dari {current.value} ke {target.value}"
        )


# ✅ Good — exception di-catch di router layer, dikonversi ke HTTP response
@router.post("/orders/{order_id}/confirm")
async def confirm_order(order_id: UUID, ...):
    try:
        order = await order_service.confirm_order(order_id, ...)
    except OrderNotFoundError:
        raise HTTPException(status_code=404, detail="Order tidak ditemukan")
    except InsufficientStockError as e:
        raise HTTPException(status_code=409, detail=f"Stok {e.sku} tidak mencukupi")
    return OrderResponse.from_orm(order)


# ❌ Bad — exception di-catch di service layer dan di-swallow
async def confirm_order(order_id):
    try:
        ...
    except Exception:
        return None  # ← caller tidak tahu apa yang salah
```

---

## Pydantic Schema (Request / Response)

```python
# ✅ Good — schema terpisah untuk request dan response
class CreateOrderRequest(BaseModel):
    channel: Literal["TOKOPEDIA", "WHATSAPP", "INSTAGRAM", "DIRECT"]
    items: list[OrderItemInput]
    voucher_code: str | None = None
    shipping_address: ShippingAddressInput

    @validator("items")
    def items_must_not_be_empty(cls, v):
        if not v:
            raise ValueError("Order harus memiliki minimal 1 item")
        return v

class OrderResponse(BaseModel):
    id: UUID
    status: OrderStatus
    total_price: Decimal
    created_at: datetime
    items: list[OrderItemResponse]

    class Config:
        orm_mode = True  # FastAPI v1, atau use_enum_values = True untuk FastAPI v2


# ❌ Bad — satu schema untuk semua keperluan, expose field internal
class OrderSchema(BaseModel):
    id: UUID
    cost_price: Decimal  # ← harga HPP tidak boleh di-expose ke client!
    internal_notes: str
    ...
```

---

## Database & Query

```python
# ✅ Good — parameterized query via SQLAlchemy ORM
async def get_orders_by_status(
    db: AsyncSession,
    status: OrderStatus,
    limit: int = 20,
    offset: int = 0,
) -> list[Order]:
    result = await db.execute(
        select(Order)
        .where(Order.status == status)
        .order_by(Order.created_at.desc())
        .limit(limit)
        .offset(offset)
    )
    return result.scalars().all()


# ✅ Good — hindari N+1 query dengan eager loading
async def get_order_with_items(db: AsyncSession, order_id: UUID) -> Order | None:
    result = await db.execute(
        select(Order)
        .options(selectinload(Order.items))  # ← load items sekalian, bukan lazy
        .where(Order.id == order_id)
    )
    return result.scalar_one_or_none()


# ❌ Bad — raw string query = SQL injection risk
async def get_order(db, order_id: str):
    return await db.execute(f"SELECT * FROM orders WHERE id = '{order_id}'")
```

---

## Keamanan

```python
# ✅ Good — secret dari environment variable via Pydantic Settings
from pydantic import BaseSettings

class Settings(BaseSettings):
    database_url: str
    redis_url: str
    jwt_secret_key: str
    midtrans_server_key: str

    class Config:
        env_file = ".env"

settings = Settings()


# ❌ Bad — hardcoded credential
DATABASE_URL = "postgresql://admin:password123@localhost/orderease"
JWT_SECRET = "supersecret"
```

```python
# ✅ Good — validasi input di boundary (router), bukan di dalam service
@router.post("/vouchers/apply")
async def apply_voucher(
    body: ApplyVoucherRequest,  # ← Pydantic validasi otomatis di sini
    current_user: User = Depends(get_current_user),
):
    ...

# Batasi rate agar tidak di-brute force
from slowapi import Limiter
limiter = Limiter(key_func=get_remote_address)

@router.post("/auth/login")
@limiter.limit("5/minute")  # ← maks 5 percobaan login per menit per IP
async def login(request: Request, body: LoginRequest):
    ...
```

---

## Testing

```python
# ✅ Good — nama test deskriptif, satu assertion per skenario, pakai fixture
# File: tests/test_orders/test_order_service.py

async def test_should_raise_insufficient_stock_when_qty_exceeds_available(
    db_session: AsyncSession,
    order_factory,
    stock_factory,
):
    # Arrange
    product = await stock_factory.create(sku="BUKU-001", available_qty=5)
    order = await order_factory.build(items=[{"sku": "BUKU-001", "qty": 10}])

    # Act & Assert
    with pytest.raises(InsufficientStockError) as exc_info:
        await order_service.confirm_order(order.id, db=db_session)

    assert exc_info.value.sku == "BUKU-001"
    assert exc_info.value.available == 5
    assert exc_info.value.requested == 10


# ❌ Bad — nama test tidak jelas, hardcoded data, test banyak skenario sekaligus
def test_order():
    result = confirm_order("123e4567-e89b-12d3-a456-426614174000")
    assert result is not None
    assert result.status == "CONFIRMED"
    assert result.total == 150000
```

**Aturan Testing:**

- Satu file test per module: `test_order_service.py`, `test_stock_repository.py`
- Nama test: `test_should_[expected]_when_[condition]`
- Coverage minimum: **80%** untuk business logic di `service.py`
- Gunakan factory (misal: `factory_boy`) untuk buat data test, bukan hardcoded UUID/angka

---

## Commit Message

Format: `[type](scope): [deskripsi singkat dalam bahasa Inggris]`

| Type       | Kapan digunakan                         | Contoh nyata                                                      |
| ---------- | --------------------------------------- | ----------------------------------------------------------------- |
| `feat`     | Fitur baru                              | `feat(orders): add voucher validation on confirm`                 |
| `fix`      | Bug fix                                 | `fix(stock): prevent negative stock on concurrent order`          |
| `refactor` | Refactor tanpa perubahan fungsionalitas | `refactor(inventory): extract StockReservation to separate class` |
| `test`     | Tambah / update test                    | `test(orders): add test for waitlisted order flow`                |
| `docs`     | Update dokumentasi                      | `docs: update ADR-002 for Redis stock lock`                       |
| `chore`    | Setup, config, dependency update        | `chore: upgrade SQLAlchemy to 2.0.30`                             |
