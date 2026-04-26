# GitHub Copilot — Global Instructions

> File ini dibaca otomatis oleh GitHub Copilot di setiap sesi.
> Tulis aturan, konvensi, dan konteks project di sini.

## Identitas Project

- **Nama**: agent_copilot
- **Tujuan**: Framework untuk memaksimalkan penggunaan AI di setiap project
- **Bahasa utama**: (sesuaikan per project)
- **Framework**: (sesuaikan per project)

## Konvensi Koding

- Gunakan bahasa Inggris untuk nama variabel, fungsi, dan komentar kode
- Gunakan bahasa Indonesia untuk komunikasi dan dzokumentasi
- Selalu tambahkan type hint / type annotation
- Ikuti prinsip SOLID dan Clean Code

## Aturan AI

- Jangan generate kode yang mengandung secret / credential hardcoded
- Selalu validasi input dari user di boundary system
- Prioritaskan keamanan sesuai OWASP Top 10
- Buat kode yang testable (dependency injection friendly)

## Struktur Response yang Diharapkan

Ketika diminta generate kode, selalu:

1. Jelaskan pendekatan singkat
2. Tulis kode yang bersih dan minimal
3. Sertakan contoh penggunaan jika diperlukan

## Referensi Tambahan

- Lihat `context/architecture.md` untuk arsitektur sistem
- Lihat `context/coding-standards.md` untuk standar koding detail
- Lihat `prompts/` untuk template prompt yang sudah disiapkan
