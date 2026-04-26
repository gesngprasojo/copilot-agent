# Workflows AI-Assisted Development

## 1. Workflow: Memulai Fitur Baru

```
1. Buka task-planning.prompt.md → Jelaskan fitur ke AI
2. AI breakdown jadi subtask → Review dan approve
3. Update context/project-context.md jika ada domain baru
4. Coding dengan Copilot inline suggestion
5. Selesai coding → Jalankan code-review.prompt.md
6. Generate test dengan test-generation.prompt.md
7. Commit dengan format konvensional
```

## 2. Workflow: Debug Issue

```
1. Copy error message + kode bermasalah
2. Jalankan debug.prompt.md
3. AI identifikasi root cause
4. Implementasi solusi yang direkomendasikan
5. Tambahkan test untuk regression
6. Catat di memory/lessons-learned.md
```

## 3. Workflow: Code Review

```
1. Pilih kode yang ingin di-review (Ctrl+Shift+P → "Ask Copilot")
2. Jalankan code-review.prompt.md
3. Address semua temuan 🔴 Kritis terlebih dahulu
4. Pertimbangkan 🟡 Warning
5. 🟢 Saran bisa dilakukan jika ada waktu
```

## 4. Workflow: Rancang Arsitektur

```
1. Jalankan architecture-design.prompt.md
2. Review diagram Mermaid yang dihasilkan
3. Update context/architecture.md
4. Share ke tim untuk feedback
```

## Tips Menggunakan Copilot Secara Efektif

- **Berikan konteks spesifik**: Sebutkan framework, pattern, dan constraint
- **Iteratif**: Mulai dari skeleton, lalu minta AI isi detail
- **Verifikasi selalu**: Jangan copy-paste tanpa membaca dan memahami
- **Update context files**: Semakin lengkap context, semakin baik output AI
- **Gunakan `#file` dan `#selection`**: Arahkan AI ke file/kode spesifik
