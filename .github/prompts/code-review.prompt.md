---
agent: ask
description: Review kode untuk bug, keamanan, dan kualitas
---

Lakukan code review menyeluruh untuk kode berikut:

**Fokus pada:**

1. **Bug & Logic Error** — Temukan potensi bug atau kesalahan logika
2. **Keamanan (OWASP Top 10)** — SQL injection, XSS, autentikasi lemah, dll
3. **Performa** — N+1 query, memory leak, kompleksitas O(n)
4. **Clean Code** — Penamaan, fungsi terlalu panjang, duplikasi
5. **Testability** — Apakah kode mudah diuji?

**Format output:**

- Gunakan emoji: 🔴 Kritis | 🟡 Warning | 🟢 Saran
- Sertakan baris kode yang bermasalah
- Berikan solusi konkret untuk setiap temuan

${selection}
