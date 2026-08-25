# Build or Upgrade a Second Brain (v2, two-mode)

> You are Claude Code or Codex. The human in front of you wants an AI-native "second
> brain" on Obsidian, with memory that survives across sessions **and writes itself**.
> They may already have one. Your first job is to find out which.
>
> Sources: spec `https://avenox.lol/beyin.md` · engine `https://github.com/avenoxai/avenoxbeyin`
> · this file adds the Windows port and the failure lessons from a real v1→v2 upgrade.

## ⛔ FIRST ACTION — do this before reading the rest

**Do not start installing. Do not create a single file yet.** Two commands, then a
question, then you know which half of this document applies to you:

```bash
uname -s 2>/dev/null || echo "Windows"
```

Ask the user, in Turkish: **"Daha önce kurulmuş bir ikinci beynin var mı? Varsa klasör
yolunu ver."** Whether they say yes or no, also scan for a folder containing `CLAUDE.md`
(or `AGENTS.md` / `GEMINI.md`) **together with** a memory folder holding `Last-Session.md`.

```bash
# inside any candidate vault
test -f .beyin-version && cat .beyin-version || echo "v1 (surum dosyasi yok)"
```

| Finding | Go to |
| --- | --- |
| No candidate found | **MODE A — fresh install** |
| Candidate, no `.beyin-version` | **MODE B — upgrade v1 → v2.** Do NOT build a new vault next to it. |
| Candidate, `.beyin-version` = `2.0.0` | Already v2 → run PHASE 6 verification only, then stop. |
| Platform is Windows | Whatever the mode, the engine section you need is **PHASE 4W**, not PHASE 4. |

Say the mode out loud in Turkish before touching anything. In MODE B add: *"Mevcut kasana
dokunmayacağım, sadece eksik katmanları ekleyeceğim."*

**Why this is the first thing and not a later phase:** an existing vault holds months of
someone's memory. Installing a fresh template over it, or beside it, is the one mistake
this document exists to prevent. If you skipped this block and already wrote files, stop
and tell the user now.

---

## The idea (say this to the user, in their own words, in Turkish)

Most people use an AI chat and it forgets them every session. A second brain fixes that:
a local Obsidian vault for everything they know and do, driven by an agent CLI, with a
memory layer that survives across sessions.

v1 did that already, but it leaned on the model **remembering** to write its memory files
at the end of a session. Whenever it forgot, that day was gone.

**v2's thesis: memory must be a mechanism, not a discipline.** Session end and
pre-compaction are caught by hooks, a small background call summarizes the conversation
into a daily log, and once a day a compiler turns those logs into linked articles. The
next morning that knowledge base is already in context. Nobody has to remember anything.

## Rules for you (read before doing anything)

1. **Speak Turkish to the user by default.** Match the language they write in.
2. **Diagnose first, build second.** Never create files before PHASE 0 is complete.
3. **Never destroy.** If a target file or folder exists, show it and ask. Default to merge
   or skip, never a silent clobber. If the user already has a brain, **their memory files
   are read-only for you** — you may add, never overwrite.
4. **Resolve every `{{PLACEHOLDER}}`** before writing. Never leave a literal `{{...}}`.
5. **Don't block on optional steps.** mem0, obsidian-cli, desktop launcher. If one fails,
   log it, say so, continue.
6. **Verify each phase** before moving on, and **never call a partial install a success.**
   If the background summarizer cannot authenticate, the system is "çalışıyor ama topal" —
   say exactly that.
7. **Zero extra cost.** Everything runs on the subscription the user already pays for,
   through `claude -p` (or `codex exec`). No API key required.

Placeholders: `{{OS_NAME}}` · `{{USER_NAME}}` · `{{USER_BIO}}` · `{{COMPANION}}` ·
`{{VAULT_PATH}}` · `{{MEMORY_DIR}}` · `{{SCOPE}}` · `{{USE_MEM0}}` · `{{TODAY}}`

---

## PHASE 0 — Diagnose: which mode are you in?

### 0.1 Platform

```bash
uname -s 2>/dev/null || echo "Windows"
```

- `Darwin` / `Linux` → the upstream bash engine runs as-is.
- Windows → **you must use the PowerShell port in PHASE 4W.** The upstream engine imports
  `fcntl`, which does not exist on Windows; its hooks are bash. Do not pretend otherwise
  and do not "try it and see" — it fails on the first line.

### 0.2 Is there an existing brain?

Ask in Turkish: **"Daha önce kurulmuş bir ikinci beynin var mı? Varsa klasör yolunu ver."**
Then scan anyway. Look for a folder that has `CLAUDE.md` (or `AGENTS.md`/`GEMINI.md`) **and**
a memory folder — commonly `🔮 850-*`, but accept any folder holding `Last-Session.md`.

For each candidate report: path, memory folder name, and version:

```bash
# inside a candidate vault
test -f .beyin-version && cat .beyin-version || echo "v1 (surum dosyasi yok)"
```

| Finding | Mode |
| --- | --- |
| No candidate | **MODE A — fresh install** |
| Candidate, no `.beyin-version` | **MODE B — upgrade v1 → v2** |
| Candidate, `2.0.0` | Already v2. Run the verification in PHASE 10 and stop. |

**Say the mode out loud in Turkish before you touch anything**, and in MODE B add: "Mevcut
kasana dokunmayacağım, sadece eksik katmanları ekleyeceğim."

---

## MODE A — Fresh install

### A.1 Interview (Turkish, conversational, not a form)

1. **İsmin ne?** → `{{USER_NAME}}`
2. **Ne iş yapıyorsun, bu beyni en çok ne için kullanacaksın?** → `{{USER_BIO}}`
3. **AI ortağına ne isim vermek istersin?** → `{{COMPANION}}`
4. **Kapsam?** → `{{SCOPE}}` — `core` (Inbox, Projects, Knowledge, Command-Center,
   companion memory, hooks, `daily/`) · `+goals` · `+money` · `+body` · `+mind` · `full`
5. **Semantik hafıza (mem0) ekleyelim mi?** Ücretsiz katman, kredi kartı istemez, dosya
   hafızasının yerine geçmez. → `{{USE_MEM0}}` (varsayılan: evet)

Derive `{{OS_NAME}}` from the machine name (PascalCase + `OS`), let them override. Pick
`{{VAULT_PATH}}` and confirm it. `{{TODAY}}` from the system date.

**`{{MEMORY_DIR}}` is `🔮 850-{{COMPANION}}`** — e.g. `🔮 850-Echo`. Tell the user once that
the persona's name lives in the folder name **and** that every hook path must match it, or
nothing gets injected. (See LESSON 1 — this is the exact failure that bit us.)

### A.2 Prerequisites

Required: an agent CLI on PATH (`claude` or `codex`) and `python3`. Optional: Obsidian
(only for the human to read the vault — the model just writes markdown), mem0.

```bash
command -v python3 >/dev/null && python3 -V || echo "python3 YOK"
command -v claude  >/dev/null && echo "claude ok" || echo "claude YOK"
```

If `python3` is missing, the vault and hooks still work but **the automatic daily log and
the compiler do not.** Call that a *degraded kurulum* in Turkish and repeat it in the final
report.

### A.3 Skeleton

```
{{OS_NAME}}/
├── 📥 000-Inbox/Dump/
├── 🎯 100-Command-Center/Dashboard.md
├── 🏰 300-Projects/
├── 🧠 500-Knowledge/          # insan yazar
│   └── Derlenen/              # MAKİNE yazar: index.md, log.md, concepts/, connections/
├── 🛠️ 600-Arsenal/
├── {{MEMORY_DIR}}/            # Core, Last-Session, Threads, Journal, Kurallar
├── daily/                     # MAKİNE yazar
├── 📦 900-Archive/
├── 📋 Templates/
├── .claude/{hooks,scripts,skills}/
└── .beyin-version             # "2.0.0"
```

Scope add-ons only if selected: `⚔️ 200-Goals`, `🔐 400-Vault`, `💪 700-Body`, `🧘 800-Mind`.

> **Why `Derlenen/` sits inside `🧠 500-Knowledge/` instead of a top-level `knowledge/`:**
> the upstream layout creates two folders whose names both mean "knowledge", and the human
> has to remember which one is theirs. Nesting keeps one entry point in Obsidian while the
> permission boundary stays exact — the compiler may only write under `Derlenen/`.
> If you keep the upstream flat layout instead, that is fine, but **say which one you chose.**

Then go to PHASE 3 (router), PHASE 4 (engine), PHASE 5 (seed), PHASE 6 (verify).

---

## MODE B — Upgrade an existing vault

This is the mode most people are actually in, and it is where damage happens. Work
**additively**. The user has months of memory in that folder.

### B.1 Snapshot first, always

```bash
cd "{{VAULT_PATH}}"
git rev-parse --is-inside-work-tree 2>/dev/null || git init -q
git add -A && git -c user.name="beyin" -c user.email="beyin@localhost" \
  commit -q -m "v2 yukseltmesi oncesi anlik goruntu" || echo "commit atlandi (degisiklik yok)"
```

If this fails, **stop and fix it before changing anything.** No snapshot, no upgrade.

### B.2 Audit — list what exists, then what is missing

Do not assume the vault matches the template. Report a table like this in Turkish:

| Katman | Var mı | Not |
| --- | --- | --- |
| Kasa iskeleti | | mevcut klasörleri **koru**, şablona zorlama |
| `{{MEMORY_DIR}}/` Core, Last-Session, Threads, Journal | | dokunma, sadece oku |
| `Kurallar.md` | | v2'de yeni |
| SessionStart / UserPromptSubmit / SessionEnd hooks | | |
| **PreCompact hook** | | v2'de yeni |
| `daily/` + flush script | | v2'nin kalbi |
| `Derlenen/` + compile script | | akşam derleyicisi |
| Health/teşhis (`health.json`, doktor skill) | | |
| `.beyin-version` | | en son yazılır |

Install **only** the missing rows. Existing hooks get **extended**, not replaced —
read the current file first and keep whatever the user added.

### B.3 The read-only rule

Their `Last-Session.md`, `Threads.md`, `Journal.md`, and `Core.md` are **inputs**, never
outputs, during an upgrade. You may append a new session entry at the end when the work is
done, exactly as the memory protocol says. You may not reformat, retitle, or "clean up"
those files. If a hook pattern doesn't match their file's headings, **change the hook, not
their file** (LESSON 1).

### B.4 Write `.beyin-version` last

Only after PHASE 6 verification passes. A vault stamped `2.0.0` while broken is worse than
one honestly stamped v1.

---

## PHASE 3 — The router (`CLAUDE.md`, and its siblings)

Keep it under ~60 lines. It is a router, not an encyclopedia. It must state:

- who `{{COMPANION}}` is, in Turkish, and that they remember across sessions
- the vault map, marking **which folders the machine owns** (`daily/`, `Derlenen/`)
- the memory protocol: at session start the hook injects; before a meaningful session ends,
  write `Last-Session.md`, update `Threads.md`, add to `Journal.md` if it mattered
- **the corrections rule:** when the user corrects you, write it into `Kurallar.md` as
  `kural:` + `neden:` — that file is injected every session
- `beyin doktor` as the thing to run when something feels wrong

If the user runs more than one agent (Claude Code + Codex + Antigravity), write the same
content into `AGENTS.md` / `GEMINI.md` and **keep them in sync from then on**. State that
rule inside the files themselves, or they drift within a week.

---

## PHASE 4 — The engine (macOS / Linux)

Fetch from the repo rather than hand-writing:

```bash
V="{{VAULT_PATH}}"
RAW="https://raw.githubusercontent.com/avenoxai/avenoxbeyin/main/template/.claude"
mkdir -p "$V/.claude/hooks/.state" "$V/.claude/scripts/.state" "$V/.claude/skills"
for F in hooks/lib.sh hooks/session-start.sh hooks/prompt-counter.sh hooks/session-end.sh \
         hooks/pre-compact.sh scripts/flush.py scripts/compile.py settings.json; do
  curl -fsSL "$RAW/$F" -o "$V/.claude/$F" || echo "EKSIK: $F"
done
chmod +x "$V/.claude/hooks/"*.sh
for H in "$V/.claude/hooks/"*.sh; do bash -n "$H" || echo "SOZDIZIMI HATASI: $H"; done
python3 -m py_compile "$V/.claude/scripts/"*.py
```

Then adjust the memory-folder name inside the hooks to `{{MEMORY_DIR}}`, and the knowledge
paths if you chose the nested layout.

---

## PHASE 4W — The engine (Windows) — **you must port, not copy**

The upstream engine will not run here. Concretely:

| Upstream | Why it breaks on Windows | Port |
| --- | --- | --- |
| `import fcntl` (both scripts) | POSIX-only, `ImportError` immediately | directory lock via `os.mkdir` — atomic everywhere; clear it by mtime if stale |
| bash hooks | not executed by the agent harness on Windows | PowerShell `.ps1`, wired in `settings.local.json` |
| `nohup … &` | no such thing | `Start-Process -WindowStyle Hidden`, prefer `pythonw.exe` so no console flashes |
| `start_new_session=True` | POSIX-only | `creationflags=DETACHED_PROCESS \| CREATE_NO_WINDOW` |
| `date -v-1d` | BSD-only | `(Get-Date).AddDays(-1)` |
| `stat.st_nlink != 1` | Windows may report `0` | reject only `> 1` |

Structure the port as: `beyinlib.py` (lock, health, atomic write, the `claude -p` call,
detached spawn) + `flush.py` + `compile.py`, and `lib.ps1` + four hook scripts.

**Two Windows-specific traps that will silently corrupt output:**

1. **Hook stdout.** PowerShell 5.1 writes stdout in the console code page (e.g. cp1254),
   so Turkish letters and emoji arrive mangled. Write raw UTF-8 bytes instead:

   ```powershell
   $bytes  = [System.Text.Encoding]::UTF8.GetBytes($json)
   $stdout = [Console]::OpenStandardOutput()
   $stdout.Write($bytes, 0, $bytes.Length); $stdout.Flush()
   ```

2. **Hook stdin and any file the Python side reads.** Read stdin as raw bytes and decode
   UTF-8 yourself; write files with `UTF8Encoding($false)` (no BOM), and read them with
   `encoding="utf-8-sig"` defensively.

Keep the upstream **security boundaries** exactly as they are — they are the best part of
the design:

- the summarizer runs in a temp directory **outside the vault**, with `--tools ""`
- the compiler runs in an isolated staging copy, and only files under the knowledge folder
  are promoted back; everything else raises `forbidden-write`
- every hook and script exits immediately if `BEYIN_INVOKED_BY` is set, so the background
  `claude -p` call cannot re-trigger the hooks

Verify the boundary rather than trusting it: write to `daily/` from inside staging and
confirm it is rejected.

---

## PHASE 5 — Seed the memory

`Core.md`, `Last-Session.md` (Genesis entry), `Threads.md`, `Journal.md`, and:

**`Kurallar.md`** — new in v2, this is what stops the same correction from repeating.
Format each entry as `**kural:**` / `**neden:**` (+ optional `**nasıl:**`) and tell the
user that a rule without a reason decays into a sentence everyone reads their own way.

> **Do not truncate this file when injecting it.** Injecting only the first N lines makes
> later rules silently invisible — the single worst failure a rules file can have. Cap by
> characters with an explicit "kırpıldı" note instead.

In MODE B: **create `Kurallar.md` only if absent**, and seed it from corrections the user
has already given you (check any per-tool memory store) — then remove those duplicates from
the tool-specific store so one rule lives in exactly one place.

---

## PHASE 6 — Obsidian recommended settings (worth doing, not just cosmetic)

> **MODE B notu:** Mevcut bir kasada bu faz büyük ihtimalle zaten yapılmış. Önce kontrol et, varsa **atla** — üzerine yazma.


Obsidian reads its config from a `.obsidian/` folder at the vault root. You can write the key
files **before** the user ever opens Obsidian, so it launches already configured instead of
making them hunt through Settings. This is proven from our own vault, not a guess.

**`{{VAULT_PATH}}/.obsidian/app.json`**
```json
{
  "alwaysUpdateLinks": true,
  "newFileLocation": "folder",
  "attachmentFolderPath": "/",
  "newFileFolderPath": "📥 000-Inbox"
}
```
Why these four matter, not just what they do:
- `alwaysUpdateLinks: true` — this vault leans hard on `[[wikilinks]]`; without this, renaming
  or moving a note silently breaks every link pointing at it. Turn it on before the vault has
  any content to lose.
- `newFileFolderPath: "📥 000-Inbox"` — matches the capture-first workflow this whole system is
  built around. A new note created ad hoc (not inside a specific folder) lands in Inbox by
  default, not scattered at vault root, waiting to be processed like everything else.
- `attachmentFolderPath: "/"` — keeps attachments at vault root instead of spawning a hidden
  folder per-note. A cosmetic-adjacent choice; if the user has a strong opinion, defer to them.

**`{{VAULT_PATH}}/.obsidian/appearance.json`**
```json
{
  "theme": "system"
}
```
`theme: "system"` follows the OS light/dark setting automatically — the sane default. Accent
color is genuinely personal taste; don't pick one for the user, leave Obsidian's default or let
them set it themselves later (Settings → Appearance).

**`{{VAULT_PATH}}/.obsidian/core-plugins.json`** — trim the plugins this vault doesn't need
rather than leaving everything on. What we run in practice:
```json
{
  "file-explorer": true,
  "global-search": true,
  "switcher": true,
  "graph": true,
  "backlink": true,
  "canvas": true,
  "outgoing-link": true,
  "tag-pane": true,
  "footnotes": false,
  "properties": true,
  "page-preview": true,
  "daily-notes": true,
  "templates": true,
  "note-composer": true,
  "command-palette": true,
  "slash-command": false,
  "editor-status": true,
  "bookmarks": true,
  "markdown-importer": false,
  "zk-prefixer": false,
  "random-note": false,
  "outline": true,
  "word-count": true,
  "slides": false,
  "audio-recorder": false,
  "workspaces": false,
  "file-recovery": true,
  "publish": false,
  "sync": true,
  "bases": true,
  "webviewer": false
}
```
`sync: true` just enables the core plugin *slot* — it doesn't mean paid Obsidian Sync is active;
that only matters if the user actually configures it. If they're syncing another way (e.g.
Syncthing between devices), this line is harmless either way.

**Dataview (community plugin — recommended, needs a manual step)**
This is the one thing you can't fully automate: community plugins are downloaded and verified
through Obsidian's own UI, and dropping raw plugin code into `.obsidian/plugins/` yourself is
fragile and not worth the risk. Instead, tell the user, once, in the first-run report (PHASE 10):

> Obsidian'da Settings → Community plugins → Turn on community plugins → Browse → "Dataview" ara
> → Install → Enable. Bunu yapınca Dashboard'daki proje tablosu otomatik çalışmaya başlar.

Once Dataview is installed, the Dashboard template can include a self-refreshing
status table instead of (or alongside) the hand-maintained project list — add this block under
a "Proje durumu (Dataview — otomatik)" heading:
````markdown
## Proje durumu (Dataview — otomatik)
Yukarıdaki liste elle güncelleniyor. Aşağıdaki tablo her projenin frontmatter'ındaki `status`
alanından kendini tazeler — biri eski kalırsa buradan yakalarsın.

```dataview
TABLE status, tags
FROM "🏰 300-Projects"
WHERE type = "project"
SORT status ASC, title ASC
```
````
If the user skips installing Dataview, this block just renders as inert text — harmless, not
worth blocking on. Mention it's there and can be activated any time later.

---

---

## PHASE 7 — Desktop launcher (brain icon 🧠)

> **MODE B notu:** Mevcut bir kasada bu faz büyük ihtimalle zaten yapılmış. Önce kontrol et, varsa **atla** — üzerine yazma.


Create a one-click desktop shortcut that opens the vault in Obsidian. Branch on `{{PLATFORM}}`.
(Works after the user has added the vault to Obsidian once — see PHASE 10.)

### macOS
```bash
# 1) Build the launcher applet
osacompile -o "$HOME/Desktop/{{OS_NAME}}.app" \
  -e 'do shell script "open \"obsidian://open?vault={{OS_NAME}}\""'

# 2) Render the 🧠 emoji to a PNG (Swift + AppKit — present on every Mac with Command Line Tools)
cat > /tmp/render_brain.swift <<'SWIFT'
import AppKit
let out = CommandLine.arguments[1]
let size = 1024.0
let img = NSImage(size: NSSize(width: size, height: size))
img.lockFocus()
let pt = size * 0.78
let font = NSFont(name: "Apple Color Emoji", size: pt) ?? NSFont.systemFont(ofSize: pt)
let s = "🧠" as NSString
let b = s.size(withAttributes: [.font: font])
s.draw(at: NSPoint(x: (size - b.width)/2, y: (size - b.height)/2), withAttributes: [.font: font])
img.unlockFocus()
if let t = img.tiffRepresentation, let r = NSBitmapImageRep(data: t),
   let p = r.representation(using: .png, properties: [:]) {
  try? p.write(to: URL(fileURLWithPath: out))
}
SWIFT
swift /tmp/render_brain.swift /tmp/brain.png

# 3) Set it as the app icon
cat > /tmp/set_icon.swift <<'SWIFT'
import AppKit
let img = NSImage(contentsOfFile: CommandLine.arguments[1])!
let ok = NSWorkspace.shared.setIcon(img, forFile: CommandLine.arguments[2], options: [])
print(ok ? "icon OK" : "icon FAILED")
SWIFT
swift /tmp/set_icon.swift /tmp/brain.png "$HOME/Desktop/{{OS_NAME}}.app"

# 4) Refresh Finder / LaunchServices
touch "$HOME/Desktop/{{OS_NAME}}.app"
/System/Library/Frameworks/CoreServices.framework/Frameworks/LaunchServices.framework/Support/lsregister -f "$HOME/Desktop/{{OS_NAME}}.app" 2>/dev/null || true
```
If `swift` is missing (no Command Line Tools), skip steps 2–3: the launcher still works, just
with the default icon. Tell the user, don't block.

### Windows
```powershell
# 1) Build a .lnk shortcut on the Desktop
$desktop = [Environment]::GetFolderPath('Desktop')
$lnkPath = Join-Path $desktop "{{OS_NAME}}.lnk"
$shell = New-Object -ComObject WScript.Shell
$shortcut = $shell.CreateShortcut($lnkPath)
$shortcut.TargetPath = "obsidian://open?vault={{OS_NAME}}"
$shortcut.Save()
```
There's no reliable built-in way to rasterize an emoji into a `.ico` on Windows the way
`AppKit` does on macOS — don't fight this. Tell the user honestly: the shortcut works and opens
straight into the vault, it just carries the default icon; if they want a custom one, they can
right-click → Properties → Change Icon and point at any `.ico` they like. Cosmetic gap, not a
functional one — don't burn time automating it.

---

---

## PHASE 8 — mem0 semantic memory (recommended, FREE — only if `{{USE_MEM0}}`)

> **MODE B notu:** Mevcut bir kasada bu faz büyük ihtimalle zaten yapılmış. Önce kontrol et, varsa **atla** — üzerine yazma.


mem0's base tier is **completely free** (no credit card). It adds a semantic-search layer on top
of the file-based memory.
1. Ensure `uv` is installed (prerequisites are covered in MODE A step A.2; on Windows:
   `irm https://astral.sh/uv/install.ps1 | iex`).
2. Get a free API key from https://mem0.ai and store it in `.claude/settings.local.json` under
   `"env": { "MEM0_API_KEY": "..." }`. **Never commit this file** — confirm
   `.claude/settings.local.json` is in `.gitignore` before this vault ever touches git.
3. Tell the user this is an upgrade layer; the file-based memory already works without it.
Keep it light — if the user skips the key, continue; the core system is fully functional.

---

---

## PHASE 9 — Multi-tool memory bridge (build even if only one tool is used today)

This phase is cheap insurance: write `{{OS_NAME}}/AI-TOOLS.md` now, once, so that if the user
ever opens this same vault with a different AI coding tool (Antigravity, Codex, Cursor,
whatever comes next), that tool can bootstrap itself from this file instead of the user
re-explaining the whole system — and, critically, without forking memory into two
disconnected copies.

**`{{OS_NAME}}/AI-TOOLS.md`**
```markdown
# AI Araçları Köprüsü

Bu dosya, {{OS_NAME}}'i Claude Code dışında bir AI aracıyla (Antigravity, Codex, Cursor,
vb.) açtığında o aracın kendi kurulumunu kendisi yapması için yazıldı.

**Kullanım:** Bu dosyayı ilgili araca ver, şunu söyle: "Bu klasörü ve bu dosyayı oku,
CLAUDE.md ve .claude/hooks/ içindeki mantığı kendi formatına çevirip kendi kurulum
dosyalarını oluştur."

## Önce yetenek kontrolü, sonra kurulum

Bu dosyayı okuyan araç, kuruluma girişmeden önce kendi neyi gerçekten otomatik yapabildiğini
kontrol etsin. Sahte/otomatikmiş gibi görünen ama garantisi olmayan bir kurulum, hiç
kurulmamaktan daha kötü — kullanıcı "hafıza otomatik güncelleniyor" sanıp kontrol etmeyi
bırakır, sonra sessizce boşluk oluşur.

- **Oturum başı/sonu hook var mı?** Varsa session-start / session-end mantığını birebir kur.
  Yoksa dürüstçe Core.md'ye not düş: "bu araçta otomatik tetikleyici yok, hafıza güncellemesi
  manuel."
- **Kural dosyası oturum başında gerçekten otomatik okunuyor mu?** Değilse aynı şekilde not düş.
- **Konuşma dökümünün yolunu (transcript) hook'a veriyor mu?** Vermiyorsa o araçta
  otomatik günlük log (flush) kurulamaz. Bunu gizleme, Core.md'ye yaz: o araçta
  ilişkisel hafızayı elle yazmak hâlâ zorunlu.
- **MCP desteği var mı?** Yoksa mem0 bağlantısını atla — dosya hafızası zaten birincil kaynak.

## Mantık: iki katman var

**1. Veri katmanı (paylaşılan, araçtan bağımsız)** — bütün `.md` notlar; makinenin
yazdığı `daily/` ve derlenen bilgi tabanı; ve özellikle
🔮 850-{{COMPANION}}/ klasörü. **BUNU ÇOĞALTMA.** Tek kopya kalmalı; iki ayrı hafıza seti
açılırsa hangi araçla konuşulduğuna göre hafıza dallanır (split-brain), "tek kaynak" fikri
biter. Kendi kurallarını/hook'unu kur ama hepsi AYNI 850-{{COMPANION}} dosyalarını okuyup yazsın.

**2. Kurulum katmanı (araca özel)** — Claude Code'da bunlar CLAUDE.md ve .claude/hooks/*.
Görevin bunların dengini kendi formatında kurmak, ama 1. katmandaki AYNI dosyalara işaret
ederek.

## Kaynak imzası

Kimlik tek: her araç kendini "{{COMPANION}}" olarak tanıtır, kullanıcıyla "Antigravity" veya
"Codex" diye konuşmaz. Ama bir tutarsızlık fark edilirse hangi aracın yazdığını geri
izleyebilmek işe yarar — bu yüzden sadece 🔮 850-{{COMPANION}}/ dosyalarında, hafif bir kaynak
notu kullanılır:
- **Journal.md / Last-Session.md** — Claude Code dışı bir araç yazdıysa satır sonuna
  `(via <araç adı>)` ekle.
- **Threads.md / Core.md** — imza gerekmez.
- **300-Projects ve diğer normal notlar** — imza yok, kaynak izi sadece hafıza dosyalarında.

## Bağlam üretimi tek yerde durmalı

Her aracın hook'u kendi bağlamını sıfırdan kurmasın. Ortak bir kütüphane dosyası yaz
(ör. `.claude/hooks/lib.*`), bağlamı üreten fonksiyon orada dursun, her aracın hook'u
onu **çağırsın**. Aynı mantığı iki dosyaya kopyalarsan orada iki hata olur: biri şimdi,
biri sonra. Gerçek vakada tam olarak bu oldu — bir desen uyuşmazlığı iki hook'a birden
kopyalanmış ve ikisi de aylarca sessizce boş dönmüştü (bkz. LESSONS).

## Kural senkronizasyon protokolü (Zero Drift)

Farklı araçlar aynı kasada çalıştığında, bir araçla yapılan kural değişikliğinin diğerlerinde
eski kalmaması için:
1. **Ortak çekirdek değişikliği** (kasa yapısı, kimlik, hafıza protokolü): değişikliği yapan
   araç tüm kural dosyalarını (CLAUDE.md ve o ana kadar kurulmuş her aracın kendi dosyası)
   aynı anda günceller.
2. **Araca özel adaptasyon**: bir aracın hook mantığı değiştiğinde, diğer araçların dengi
   dosyaları da eşzamanlı uyarlanır.
3. **Bilinmeyen/karmaşık durum**: 🔮 850-{{COMPANION}}/Threads.md içine tek satırlık açık iş
   düşülür, sıradaki araç açıldığında ilk iş bunu tamamlar.
```

---

---

## PHASE 10 — Verify honestly, then report

Run these, and treat each as pass/fail, not vibes:

1. Hook files exist and are wired in settings.
2. `BEYIN_INVOKED_BY` guard present in every hook.
3. `python3` and the agent CLI on PATH.
4. **The background model call actually authenticates** (LESSON 2):
   ```bash
   echo "yalnizca su kelimeyi yaz: TAMAM" | claude -p --model haiku --output-format text --safe-mode --tools ""
   ```
5. **Hook patterns match the real headings in the memory files** (LESSON 1).
6. End-to-end: run the session-start hook and read its JSON — are the sections there, and
   is the mojibake count zero?
7. Compiler dry run: `python3 .claude/scripts/compile.py --dry-run`.

Then report in Turkish: what was installed, **where**, and **when it runs**; what was
skipped and why; and the honest verdict — *sağlam* / *çalışıyor ama topal* / *bozuk*.

Install a `beyin-doktor` skill that re-runs all of this on demand, including check 5.

---

### Kalıcı kullanım kuralı — bundan sonra HER sohbette bunu yap

Bu kısmı kurulum bittikten sonra ayrı, vurgulu bir mesaj olarak söyle — bir kerelik bilgi değil,
kullanıcının bundan sonra her oturumda uyması gereken bir alışkanlık:

> Kurulum bitti. Ama sürekliliğin çalışması için her seferinde iki şeye dikkat etmen lazım:
>
> **1) Her yeni konuşmaya bu klasörü açarak başla.** İster terminalden (`{{VAULT_PATH}}`
> klasörüne gir, `claude` yaz), ister Claude Code masaüstü uygulamasından (o klasörü proje
> olarak seç) — hangisini kullanırsan kullan fark etmez, önemli olan açılan projenin bu vault
> klasörü olması. Başka bir klasörden ya da genel bir sohbetten başlarsan CLAUDE.md okunmaz,
> hafıza hook'ları tetiklenmez, {{COMPANION}} seni hatırlamıyormuş gibi davranır — bozuk değil,
> sadece yanlış yerden konuşuyorsundur.
>
> **2) Konuşma bittiğinde oturumu düzgün kapat.** Terminalde `/exit` yaz; masaüstü uygulamasında
> sohbeti kapat ya da yeni bir sohbete geç. Bu, hafızayı güncellemeyi unuttuysan seni uyaran
> bitiş kontrolünü (session-end hook) tetikler. Pencereyi öylece kapatıp gitmek de çoğu zaman
> işe yarar ama garantisi yoktur — `/exit` ya da açık bir "sohbeti bitir" güvenilir olandır.
>
> Bu iki adımı atlarsan sistem hâlâ çalışır ama süreklilik zayıflar — bir sonraki oturum "nerede
> kaldık"ı bilmeyerek başlar. Alışkanlık haline gelmesi birkaç oturum sürer, sorun değil.

---

---

## PHASE 11 — Vaultu derinleştirme (yalnızca geçmiş projesi olan kullanıcılar için)

> **MODE B notu:** Yükseltme yapıyorsan bu faz zaten geçmişte yapılmış olabilir;
> kasada içerik varsa atla.


İskelet kuruldu ve doğrulandı. Bu faz, kullanıcının PHASE 0.2'de belirttiği eski birikimini
(`{{OLD_PROJECTS_PATH}}`) boş tuvalin üzerine entegre etmek için. Bunu **aynı oturumda, otomatik
olarak başlatma** — kullanıcıya sor, çünkü bu, iskelet kurulumundan çok daha fazla judgment call
içeriyor ve genelde ayrı bir oturum olarak ele alınması daha sağlıklı.

### 11.1 Önce iki soru sor
1. **Model/efor:** Bu iş çok dosya tarayıp anlamlandıracak ve doğru kasa bölümlerine
   (300-Projects, 600-Arsenal, 500-Knowledge vb.) yerleştirecek — kullanıcıya hangi model/efor
   ile devam etmek istediğini sor, optimum verim/token kullanımı onun tercihi. Kendi başına
   varsayma.
2. **Sıralama:** Kullanıcıya açıkça söyle — **tek seferde her şeyi derinlemesine doldurmaya
   çalışma.** Önce iskeleti kur (hangi proje nereye gidiyor, temel bir proje listesi/dashboard),
   sonra ayrı oturumlarda bölüm bölüm derinleştirin (önce Projeler, sonra Arsenal/Kaynaklar,
   sonra varsa Vizyon, sonra varsa Vault). Tek oturumda hepsini bitirmeye çalışmak hem contexti
   şişirir hem de derinliği yüzeysel bırakır.

### 11.2 Taramada dikkat edilecekler
- **Yedeksiz/commit'lenmemiş bir proje bulursan** (özellikle çok sayıda commit içeren ama
  hiçbir uzak depoya push edilmemiş bir şey), kullanıcı "sil" dese bile önce göster, önce
  yedekle (örn. private GitHub reposuna push), sonra sil. Onay alınmış olması sırayı atlamak
  için gerekçe değil — bkz. Rule 3.
- **Kullanıcının kendi eski sitesi/CV'si gibi tek bir dış kaynaktan** bio/uzmanlık bilgisi
  çekiyorsan, olduğu gibi yazma. Kullanıcıya sor, özellikle güncelliğinden emin olmadığın
  detaylarda (eski şirket adı, tamamlanmamış proje vb.) — bkz. Rule 9.
- **Dosyalama ile derin işi karıştırma.** Bir projeyi doğru klasöre taşımak, etiketlemek, temel
  bir not açmak — sorulmadan yapılabilir. O projeyi araştırıp uzun, otoriter görünen bir analiz
  yazmak, ya da hangi projenin "aktif" hangisinin "duraklatıldı" olduğuna kendi başına karar
  vermek — sorulmadan yapılmaz. Emin değilsen notu `status: taslak` ile bırak, kaynağı ve
  neyin eksik olduğunu yaz, kullanıcıya sor. (Bunu biz bir Unity notu üzerinde atlayıp geri
  almak zorunda kaldık — bkz. Rule 8.)
- **Her yeri doldurmak amaç değil.** Bazı bölümler (kişisel yansımalar, henüz netleşmemiş
  hedefler) organik dolsun diye bilerek boş bırakılabilir. Kullanıcıya bunun bir tercih
  olduğunu söyle, eksik gibi sunma — bkz. Rule 10.

### 11.3 Bittiğinde
Normal PHASE 3 hafıza protokolünü uygula: Last-Session.md ve Threads.md'yi bu entegrasyonun
özetiyle güncelle — ne taşındı, ne kasıtlı olarak boş/taslak bırakıldı, sıradaki bölüm hangisi.

---

---

## PHASE 12 — Cihazlar arası senkron (yalnızca `{{USE_SYNC}}` ise)

> ⚠️ **Senkron kurarken motorun state klasörlerini mutlaka dışla.**
> `.claude/hooks/.state`, `.claude/scripts/.state` ve varsa araç başına state
> klasörleri cihaza özeldir. Başka bir makineden gelen `compile-trigger-<tarih>`
> damgası derleyiciye "bugün zaten çalıştım" dedirtip günü **sessizce**
> atlatır. Kilit dosyaları için de aynısı geçerli. (Bkz. LESSONS.)


Sadece kullanıcı PHASE 0.2'de birden fazla cihaz kullandığını söylediyse yap. Araç: **Syncthing**
— ücretsiz, açık kaynak, cihazdan cihaza (P2P) senkronize eder, bulut kullanmaz, dosya geçmişi
tutmaz (silinen dosya her iki tarafta da silinir, bir "sürüm geçmişi" değildir — kullanıcıya
bunu baştan söyle). Bunu BilalOS'ta PC+Android arasında zaten kurup test ettik, çalışıyor; bu
faz onu macOS+Windows(+telefon) kombinasyonuna genişletiyor.

**Mantık her cihazda aynı:** Syncthing'i kur → vault klasörünü paylaşılan klasör olarak ekle →
cihazları birbirine "Device ID" ile tanıt → karşı taraftan gelen paylaşım isteğini kabul et →
bir dosyada değişiklik yapıp diğer cihazda göründüğünü doğrula.

### 12.1 macOS kurulumu
```bash
# Homebrew ile resmi macOS uygulaması
brew install --cask syncthing-app
```
Alternatif: https://github.com/syncthing/syncthing-macos üzerinden DMG indirip sürükle-bırak
kurulum (Homebrew yoksa). Kurulumdan sonra uygulamayı aç, arayüz tarayıcıda açılır
(`localhost:8384`) — "Add Folder" ile `{{VAULT_PATH}}`'i ekle.

### 12.2 Windows kurulumu
**SyncTrayzor** kullan — Syncthing'i sistem tepsisinde (system tray) çalıştıran bir sarmalayıcı,
Windows'ta çıplak Syncthing'den daha rahat. https://github.com/canton7/SyncTrayzor/releases
üzerinden son sürümü indir, kur. Kurulumdan sonra tepsi ikonundan arayüzü aç, "Add Folder" ile
`{{VAULT_PATH}}`'i ekle. (Bu, BilalOS'ta zaten doğrulanmış kurulum yolu.)

### 12.3 Telefon (opsiyonel üçüncü cihaz)
- **Android:** **Syncthing-Fork** (F-Droid veya GitHub releases) — resmi Google Play uygulaması
  2024'te geliştirici tarafından bırakıldı, topluluk forku aktif olan bu. "Resmi" gibi kesin
  ifadeler kullanma, bu ekosistem hızlı değişiyor; kurulum anında güncel olanı bir kez teyit et.
- **iOS:** **Möbius Sync** (App Store) — üçüncü parti bir istemci, Syncthing motorunu içinde
  çalıştırıyor. Uygulamanın kendi sanal alanı (sandbox) içinde **20MB'a kadar ücretsiz**; bir
  Obsidian vault'u muhtemelen bunu aşar, kullanıcıya tek seferlik satın alma gerekebileceğini
  baştan söyle, sürpriz yapma.

### 12.4 Cihazları birbirine tanıtma (her platformda aynı akış)
1. Her cihazda Syncthing arayüzünü aç, "Actions → Show ID" (ya da "This Device" kartı) ile o
   cihazın Device ID'sini al.
2. Cihaz A'da "Add Remote Device" ile cihaz B'nin ID'sini gir; cihaz B'de gelen isteği kabul et.
   Aynısını her cihaz çifti için tekrarla (3 cihaz varsa 3 çift).
3. Vault klasörünü paylaştığın her cihazda, karşı taraftan gelen "bu klasörü paylaşmak istiyor"
   isteğini kabul et ve yerel yolunu onayla.
4. **Doğrula:** bir cihazda bir notu değiştir, birkaç saniye/dakika içinde diğer cihazda
   değiştiğini gör. Bunu söylemekle yetinme, kullanıcıyla birlikte gerçekten test et.

### 12.5 Kullanıcıya baştan söylenecek gerçek sınır
Syncthing gerçek zamanlı ortak düzenleme (Google Docs gibi) yapmaz. İki cihaz birbirinden
kopukken (biri kapalıyken) **aynı notu** ikisinde de değiştirirsen, Syncthing sessizce
birleştirmez — bir `.sync-conflict-...` dosyası oluşturur, iki sürüm de yanyana durur. Nadir
ama olabilir; kullanıcı bunu görürse paniklemesin diye baştan bir kez söyle.

---

---

## LESSONS — real failures from an actual v1 to v2 upgrade (2026-08-25)

These are not hypotheticals. Each one shipped and stayed silent for a while.

**LESSON 1 — A hook that finds nothing looks exactly like a hook with nothing to find.**
The session-start hook searched for `## Session:`. The vault's file said `## Oturum:`
(Turkish). Both were written by the same assistant, months apart, and nobody compared them.
Result: the "what did we do last time" block was **never injected for months** — and it
looked normal, because an empty section reads as "nothing to carry over."
→ Match both languages, and make the doctor **compare hook patterns against the actual
headings** in the memory files. Also: if the same context-building logic is copied into a
second tool's hook, this bug gets written twice. Keep it in **one shared library** that
every tool's hook calls.

**LESSON 2 — The background call has its own identity.**
The agent session in front of you may be authenticated by a desktop host while the CLI's
own credentials are expired. `claude -p` then fails with *"OAuth session expired"*, the
hook still exits 0, and nothing is ever written. Test the actual call (check 4). The fix is
for the user to run `claude` in a terminal and `/login`.

**LESSON 3 — Silence is the default failure mode; build against it.**
Write every failure into a `health.json` and surface it at session start. Equally
important: **clear it after a success**, or a fixed problem keeps warning for days and the
user learns to ignore warnings.

**LESSON 4 — Sync tools and lock files are enemies.**
If the vault is synced (Syncthing, iCloud, Dropbox), exclude the engine's state folders.
A `compile-trigger-<date>` stamp copied from another machine makes the compiler think it
already ran and skip the day — silently.

**LESSON 5 — Don't ask where a rule lives twice.**
If the tool has its own memory store *and* the vault has `Kurallar.md`, the same rule ends
up in both and they drift. Pick the vault: every tool can read it, and the hook injects it.
Leave only a pointer in the tool-specific store.

---

## Credits

Concept and v2 spec: Avenox — `https://avenox.lol/beyin.md`,
engine `https://github.com/avenoxai/avenoxbeyin`.
Knowledge-compilation architecture follows Andrej Karpathy's LLM knowledge-base pattern:
`https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f`.
Windows port, two-mode structure and the lessons above: collected while building and
upgrading a real vault (BilalOS, https://github.com/bilalfarukozdemir).
