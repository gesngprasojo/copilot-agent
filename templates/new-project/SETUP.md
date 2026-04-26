# New Project Setup Checklist

> Salin folder ini ke setiap project baru. Isi setiap file sesuai konteks project.

## Langkah Setup (jalankan sekali per project baru)

### 1. Konfigurasi AI Context
- [ ] Isi `context/project-context.md` — domain bisnis, terminologi, aturan bisnis
- [ ] Isi `context/architecture.md` — arsitektur sistem, diagram, keputusan teknis
- [ ] Isi `context/coding-standards.md` — sesuaikan dengan bahasa/framework project
- [ ] Update `.github/copilot-instructions.md` — nama project, bahasa utama, framework

### 2. Setup VS Code
- [ ] Copy `.vscode/settings.json` ke project
- [ ] Copy `.vscode/extensions.json` ke project
- [ ] Install recommended extensions

### 3. Setup Prompts
- [ ] Copy `.github/prompts/` ke project
- [ ] Sesuaikan prompt jika ada kebutuhan spesifik

### 4. Inisialisasi Memory
- [ ] Buat `memory/lessons-learned.md` untuk project ini
- [ ] Catat keputusan teknis awal

## Struktur yang Perlu Ada di Setiap Project

```
my-project/
├── .github/
│   ├── copilot-instructions.md   ← WAJIB
│   └── prompts/                  ← WAJIB
├── .vscode/
│   ├── settings.json             ← Direkomendasikan
│   └── extensions.json           ← Direkomendasikan
├── context/
│   ├── project-context.md        ← WAJIB
│   ├── architecture.md           ← WAJIB
│   └── coding-standards.md       ← WAJIB
└── memory/
    └── lessons-learned.md        ← Direkomendasikan
```
