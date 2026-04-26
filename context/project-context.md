# Project Context

> File ini memberikan konteks domain kepada AI agar respons lebih relevan.
> Isi sesuai dengan project yang sedang dikerjakan.

## Domain Bisnis

**Industri**: [e-commerce / fintech / healthcare / SaaS / dll]

**Problem yang diselesaikan**:
> Jelaskan masalah bisnis yang diselesaikan sistem ini dalam 2-3 kalimat.

**User utama**:
- `[Persona 1]` — [apa yang mereka lakukan di sistem ini]
- `[Persona 2]` — [apa yang mereka lakukan di sistem ini]

## Terminologi Domain (Ubiquitous Language)

> Gunakan istilah-istilah ini secara konsisten di kode, dokumentasi, dan komunikasi.

| Istilah        | Definisi                                           |
|----------------|----------------------------------------------------|
| `[Term A]`     | [Definisi spesifik dalam konteks bisnis ini]       |
| `[Term B]`     | [Definisi spesifik dalam konteks bisnis ini]       |

## Aturan Bisnis Kritis

1. [Aturan bisnis #1 — misal: "Diskon tidak bisa melebihi 80% dari harga normal"]
2. [Aturan bisnis #2]
3. [Aturan bisnis #3]

## Integrasi Eksternal

| Sistem     | Tujuan             | Protokol  | Environment Variable  |
|------------|--------------------|-----------|-----------------------|
| [Sistem A] | [Untuk apa?]       | REST/gRPC | `SYSTEM_A_API_KEY`    |
| [Sistem B] | [Untuk apa?]       | Webhook   | `SYSTEM_B_SECRET`     |

## Hal yang TIDAK boleh dilakukan AI

- Jangan generate migrasi database secara otomatis tanpa review
- Jangan ubah skema tabel `[nama_tabel]` — ada dependency legacy
- Jangan hapus field yang sudah ada — bisa merusak backward compatibility
