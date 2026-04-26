# agent_copilot

Framework untuk memaksimalkan penggunaan AI (GitHub Copilot) di setiap project.

## Struktur Direktori

```
agent_copilot/
│
├── .github/
│   ├── copilot-instructions.md        # Instruksi global untuk Copilot (dibaca otomatis)
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
├── context/                           # 🧠 "Otak" AI — baca ini sebelum coding
│   ├── project-context.md             # Domain bisnis, terminologi, aturan bisnis
│   ├── architecture.md                # Arsitektur sistem, diagram, keputusan teknis
│   └── coding-standards.md            # Konvensi koding, naming, error handling
│
├── memory/                            # 📝 Catatan lintas sesi
│   ├── lessons-learned.md             # Pelajaran & keputusan teknis dari project
│   └── session-template.md            # Template untuk mencatat progress sesi kerja
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
Salin file-file wajib ke project Anda dan isi context:
```bash
cp -r agent_copilot/.github/prompts   your-project/.github/
cp -r agent_copilot/context           your-project/
cp    agent_copilot/.vscode/settings.json  your-project/.vscode/
```

### 2. Isi Context Files
Semakin detail context yang Anda berikan, semakin relevan saran AI:
- `context/project-context.md` — domain bisnis & aturan bisnis
- `context/architecture.md` — arsitektur & keputusan teknis
- `context/coding-standards.md` — konvensi koding project

### 3. Gunakan Custom Prompts
Di VS Code dengan Copilot Chat:
- Ketik `/` untuk melihat daftar prompt tersedia
- Atau buka Command Palette → "Chat: Run Prompt"

### 4. Update Memory Secara Berkala
Setelah menemukan gotcha atau membuat keputusan penting:
- Catat di `memory/lessons-learned.md`
- AI akan membaca ini di session berikutnya

## Referensi

- [VS Code Custom Instructions](https://code.visualstudio.com/docs/copilot/copilot-customization)
- [Copilot Prompt Files (.prompt.md)](https://code.visualstudio.com/docs/copilot/copilot-customization#_reusable-prompt-files-experimental)
