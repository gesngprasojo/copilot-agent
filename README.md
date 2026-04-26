# agent_copilot

Framework untuk memaksimalkan penggunaan AI (GitHub Copilot) di setiap project.

> Semua file `context/` dan `memory/` sudah diisi dengan contoh nyata menggunakan domain **OrderEase**
> (SaaS manajemen pesanan UMKM berbasis Python/FastAPI + PostgreSQL + Redis).
> Gunakan sebagai referensi, lalu sesuaikan dengan project Anda.

## Struktur Direktori

```
agent_copilot/
│
├── .github/
│   ├── copilot-instructions.md        # Instruksi global untuk Copilot (dibaca otomatis)
│   ├── agents/                        # Custom agent dengan persona & tool khusus
│   │   └── add-module-feature.agent.md  # Agent: tambah fitur baru (edit 5 file sekaligus)
│   └── prompts/                       # Custom prompt siap pakai (Ctrl+Shift+P → "Run Prompt")
│       ├── code-review.prompt.md      # Review kode: bug, keamanan, clean code
│       ├── test-generation.prompt.md  # Generate unit test komprehensif
│       ├── refactor.prompt.md         # Refactor kode dengan prinsip SOLID
│       ├── documentation.prompt.md    # Buat docstring / dokumentasi teknis
│       ├── debug.prompt.md            # Debug & temukan root cause error
│       ├── task-planning.prompt.md    # Breakdown fitur jadi subtask teknis
│       └── architecture-design.prompt.md  # Rancang arsitektur sistem
│
├── .vscode/
│   ├── settings.json                  # Konfigurasi VS Code + Copilot yang optimal
│   └── extensions.json                # Daftar extension yang direkomendasikan
│
├── context/                           # 🧠 "Otak" AI — diisi contoh nyata domain OrderEase
│   ├── project-context.md             # Domain bisnis, terminologi, aturan bisnis, integrasi eksternal
│   ├── architecture.md                # Stack teknologi, diagram arsitektur, ADR, struktur direktori
│   └── coding-standards.md            # Naming convention, contoh ✅/❌ per pattern, commit message
│
├── memory/                            # 📝 Catatan lintas sesi — diisi contoh nyata OrderEase
│   ├── lessons-learned.md             # Keputusan teknis, gotcha & pitfall, perintah penting
│   └── session-template.md            # Template sesi kerja + contoh sesi yang sudah selesai
│
├── workflows/
│   └── development-workflow.md        # SOP pengembangan dengan bantuan AI
│
└── templates/
    └── new-project/
        └── SETUP.md                   # Checklist setup AI untuk project baru
```

## Cara Pakai

### 1. Setup Project Baru

Salin file-file wajib ke project Anda:

```bash
cp -r agent_copilot/.github/prompts        your-project/.github/
cp -r agent_copilot/context                your-project/
cp -r agent_copilot/memory                 your-project/
cp    agent_copilot/.vscode/settings.json  your-project/.vscode/
```

### 2. Sesuaikan Context Files

File sudah berisi contoh lengkap domain OrderEase — ganti dengan konteks project Anda:

- `context/project-context.md` — ganti domain bisnis, terminologi, dan aturan bisnis
- `context/architecture.md` — ganti stack teknologi, diagram, dan ADR sesuai arsitektur Anda
- `context/coding-standards.md` — sesuaikan naming convention dan contoh kode per bahasa/framework

### 3. Sesuaikan Memory Files

- `memory/lessons-learned.md` — kosongkan contoh OrderEase, mulai isi dengan keputusan teknis project Anda
- `memory/session-template.md` — gunakan template yang tersedia, hapus contoh sesi OrderEase

### 4. Gunakan Custom Prompts

Di VS Code dengan Copilot Chat:

- Ketik `/` untuk melihat daftar prompt tersedia
- Atau buka Command Palette → "Chat: Run Prompt"

### 5. Update Memory Secara Berkala

Setelah menemukan gotcha atau membuat keputusan penting:

- Catat di `memory/lessons-learned.md` dengan format `[YYYY-MM-DD] — [pelajaran]`
- AI akan membaca ini di session berikutnya sebagai konteks tambahan

## Referensi

- [VS Code Custom Instructions](https://code.visualstudio.com/docs/copilot/copilot-customization)
- [Copilot Prompt Files (.prompt.md)](https://code.visualstudio.com/docs/copilot/copilot-customization#_reusable-prompt-files-experimental)
