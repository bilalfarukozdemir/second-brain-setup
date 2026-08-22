# beyin.md — Build Me a Second Brain (BilalOS tecrübesiyle genişletilmiş sürüm)

> You are Claude Code. The human in front of you wants their own AI-native "second brain"
> running on Obsidian + Claude Code, with **persistent memory across sessions**. This file
> is your complete, self-contained build spec — the original open-source scaffold plus
> hard-won lessons from a real deployment (BilalOS). Read it once, top to bottom, then
> execute it phase by phase. You need no other context.
>
> Original source: https://avenox.lol/beyin.md — this version adds: cross-platform hooks
> (macOS/Linux **and** Windows), Obsidian recommended settings, a multi-tool memory bridge
> phase, a "deepen the vault" phase for users who bring pre-existing projects, and a
> cross-device sync phase.

---

## The idea (say this to the user, in their own words)

Most people use ChatGPT and it forgets them every session. A second brain fixes that:
a local Obsidian vault for everything you know and do, driven by Claude Code, with a memory
layer that survives across sessions. You don't manage files — you talk to it, and it organizes,
remembers, and builds on yesterday. This script sets the whole thing up for you, start to finish,
and — if the user already has old projects lying around — helps integrate that history too.

### Before you start: set expectations honestly

Say this to the user, in your own words, before PHASE 0 begins — don't skip it and don't bury
it in the middle of a long response:

> Bu kurulum uzun bir süreç. Tek mesajda bitecek bir şey değil — bugün iskeleti kuracağız,
> sonra ayrı oturumlarda (Projeler, Arsenal, Vizyon gibi) bölüm bölüm derinleştireceğiz, üstüne
> istersen cihazlar arası senkron ve başka araçlarla köprü gibi ek adımlar da var. Sıkıcı gelen
> anlar olabilir — bir klasör ismi onaylamak, bir API key yapıştırmak gibi küçük mekanik adımlar
> da var arasında. Ama sonunda gerçekten hatırlayan, senin geçmişini bilen bir sistemin oluyor —
> buna değer. Azimle, adım adım gidelim; bir fazda takılırsak atlayıp devam ederiz, hiçbir şey
> tek seferde mükemmel olmak zorunda değil.

This isn't filler politeness — it's calibration. A user who expects one quick prompt and gets a
multi-session build will feel misled halfway through and may abandon it. A user who knows
upfront that this is a marathon, not a sprint, sticks with it.

---

## Rules for you, Claude (read before doing anything)

1. **Speak Turkish to the user by default.** The audience is Turkish. Match the language the
   user writes in, but default to warm, direct Turkish. (This file is in English only so your
   instructions stay precise — the *system you build* talks to them in Turkish.)
2. **Interview first, build second.** Do PHASE 0 before touching the filesystem.
3. **Never destroy — and never take "delete it" at face value.** If a target folder/file
   already exists, show it and ask before overwriting. Default to merge/skip, never silent
   clobber. This applies doubly during PHASE 11 (deepening): if you find an old project with
   no backup and no git history, **back it up first** (e.g. push it private to GitHub) even
   if the user tells you to just delete it — show them what you found, let them confirm the
   backup happened, then proceed. A user saying "sil" (delete) once doesn't mean they've
   weighed what's actually in that folder.
4. **Resolve every `{{PLACEHOLDER}}`** from the interview before writing files. Never leave a
   literal `{{...}}` in any generated file.
5. **Don't block on optional steps.** Obsidian CLI, mem0, and analytics are nice-to-haves.
   If an install fails, log it, tell the user, and continue.
6. **Verify each phase — and actually test the things that claim to be automatic.** A quick
   file-existence check before moving on is the minimum. For the memory hooks specifically
   (PHASE 4), that's not enough: after wiring them, tell the user to close this session and
   start a fresh one, and confirm the injected context genuinely shows up. A hook that looks
   correct on paper but was never fired is worse than no hook — it gives false confidence and
   the user stops double-checking.
7. **Be the demo.** This is often filmed or screen-shared. Narrate what you're doing in short
   Turkish lines as you go ("Vault iskeletini kuruyorum...", "Hafıza motorunu bağlıyorum...").
8. **Filing ≠ deep work.** This distinction matters most in PHASE 11, but state it as a
   standing rule in the CLAUDE.md you write (PHASE 3), because it governs everything that
   happens after this setup session ends. Moving a note to the right folder, tagging it,
   linking it — low risk, do it without asking. Researching a topic, making a judgment call,
   or writing long authoritative-sounding content — high risk, needs the user's go-ahead. If
   you're mid-task and unsure which one you're doing, default to the cheap version: file it
   with `status: taslak`, note the source and what's missing, and ask before going deeper.
9. **One external source is not ground truth.** If you pull bio/expertise/project data from
   the user's own old website, CV, or README to seed the vault, don't transcribe it blindly —
   confirm with the user first, especially anything that might be stale (people change jobs,
   rebrand projects, abandon side-projects). The person's own current word outranks any
   document about them, including ones they wrote themselves six months ago.
10. **Filling every folder is not the goal.** Some sections (reflections, personal notes,
    anything meant to grow organically) should stay empty at first. Say so explicitly when
    you get there instead of generating filler content to make the vault look complete. An
    intentionally empty section is a decision, not a gap.

Placeholders you must resolve:
`{{OS_NAME}}` · `{{USER_NAME}}` · `{{USER_BIO}}` · `{{COMPANION}}` · `{{VAULT_PATH}}` ·
`{{SCOPE}}` · `{{USE_MEM0}}` · `{{PLATFORM}}` (mac/linux or windows) · `{{TODAY}}` (YYYY-MM-DD) ·
`{{OLD_PROJECTS_PATH}}` (can be empty) · `{{USE_SYNC}}` + `{{SYNC_DEVICES}}` (can be empty/no)

---

## FAST PATH — clone the open-source template (recommended)

There's a ready-made, personal-data-free scaffold of this whole system on GitHub. Cloning it is
faster and less error-prone than building every file by hand. **Prefer this path if the network
and git are available**, then layer PHASE 6B (Obsidian settings), PHASE 9 (multi-tool bridge),
PHASE 11 (deepening), and PHASE 12 (cross-device sync) from this document on top of it — those
aren't in the upstream scaffold yet.

```bash
git clone https://github.com/avenoxai/avenoxbeyin.git /tmp/avenoxbeyin && cd /tmp/avenoxbeyin
```
Then read and follow `SETUP.md` in that repo — it contains the full interview + personalize +
launcher runbook (the scaffold is already in `template/`, so you only copy and fill in the blanks).

If the clone fails (no network/git), fall back to building from scratch with the PHASES below.
Both produce the same system.

---

## PHASE 0 — Discover & interview (from-scratch fallback)

### 0.1 Detect the platform, then the machine name → derive the OS name

You're already running inside the user's native shell, which tells you the platform for free:
if you're executing bash, you're on macOS/Linux; if you're executing PowerShell, you're on
Windows. Set `{{PLATFORM}}` accordingly — every later phase branches on it.

```bash
# macOS/Linux
scutil --get ComputerName 2>/dev/null || hostname
```
```powershell
# Windows
$env:COMPUTERNAME
```
Turn the computer name into a clean PascalCase brand and append `OS`. Strip "MacBook",
"Pro", "Air", "iMac", "DESKTOP-", "'s", apostrophes, dashes.
- `Johns-MacBook-Pro` → `JohnOS`
- `aylin's Mac` → `AylinOS`
- `DESKTOP-AB12` → `Ab12OS` (fallback)

Propose `{{OS_NAME}}` to the user and let them override. This is the name of their whole system
(folder, dashboard title, the vault).

### 0.2 Ask exactly these questions (Turkish, conversational — not a form)
1. **İsmin ne?** → `{{USER_NAME}}`
2. **Ne iş yapıyorsun / bu beyni en çok ne için kullanacaksın?** (1-2 cümle) → `{{USER_BIO}}`
3. **AI ortağına ne isim vermek istersin?** (Avenox'unki "Echo") → `{{COMPANION}}`
4. **Kapsam — neye ihtiyacın var?** Pick the folders to create → `{{SCOPE}}`:
   - `core` (herkes): Inbox, Knowledge, Projects, Command-Center, companion memory, hooks
   - `+money`: finans/varlık takibi (🔐 Vault)
   - `+body`: sağlık/antrenman (💪 Body)
   - `+goals`: hedefler/OKR (⚔️ Goals)
   - `+mind`: notlar/yansımalar (🧘 Mind)
   - `full`: hepsi **(Önerilen)** — kapsamı sonradan genişletmek, dar başlayıp klasör eklemekten
     daha kolay. Baştan `full` seçip boş kalan bölümleri organik dolmaya bırakmak (bkz. Rule 10)
     dar başlayıp sonra "aslında Vizyon da lazımmış" diye geri dönmekten daha az sürtünmeli.
5. **Semantik hafıza (mem0) ekleyelim mi?** Açıkla: dosya-tabanlı hafıza API'siz çalışır ve
   herkese yeter. mem0 üstüne "anlamsal arama" katmanı koyar — **temel sürümü tamamen ücretsiz**
   (mem0.ai'den ücretsiz API key, kredi kartı yok). **(Önerilen)** → `{{USE_MEM0}}` (default: evet)
6. **Geçmiş proje birikimin var mı?** Bu kişi yapay zekayla çalışmaya yeni başlamıyor olabilir —
   varsa nerede durduğunu şimdiden sor (dosya yolu), ama entegrasyonu şimdi yapma, sadece not al.
   Gerçek entegrasyon PHASE 11'de, iskelet kurulduktan **sonra**, ayrı bir adım olarak yapılacak.
   → `{{OLD_PROJECTS_PATH}}` (boş olabilir)
7. **Birden fazla cihazda mı kullanacaksın?** (ör. hem Mac hem Windows, + telefon). Öyleyse
   cihazlar arası senkron (Syncthing, ücretsiz, P2P, bulut yok) kurmayı öner — **(Önerilen, eğer
   birden fazla cihaz varsa)**. Tek cihaz kullanıyorsa bu adımı tamamen atla, gereksiz karmaşıklık
   katma. → `{{USE_SYNC}}` + `{{SYNC_DEVICES}}` (ör. "macOS + Windows + iPhone"). Gerçek kurulum
   PHASE 12'de, iskelet ve (varsa) PHASE 11 bittikten sonra yapılır.

### 0.3 Pick the vault location → `{{VAULT_PATH}}`
- **macOS, Obsidian + iCloud var:** `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/`
  varsa default olarak `.../Documents/{{OS_NAME}}` öner (cihazlar arası senkron sağlar).
- **macOS/Linux, iCloud yok:** `~/Documents/{{OS_NAME}}`.
- **Windows:** `~/Documents/{{OS_NAME}}` öner. Kullanıcının OneDrive/senkron klasörü altında bir
  yol söylerse **itiraz etme ve "sorun olur" deme** — sadece yolu olduğu gibi kullan ve gerçekten
  var olduğunu doğrula. Senkron davranışı hakkında varsayımda bulunma; bu kullanıcının kendi
  bileceği bir tercih.
- Her durumda: **yolu kullanıcıyla teyit et**, sessizce seçip ilerleme.

Set `{{TODAY}}`:
```bash
date +%F
```
```powershell
Get-Date -Format 'yyyy-MM-dd'
```

---

## PHASE 1 — Prerequisites

Check each; install only what's missing. Narrate progress. Branch on `{{PLATFORM}}`.

### macOS/Linux
```bash
# Homebrew (macOS package manager) — required
command -v brew >/dev/null || /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Obsidian (the knowledge app) — required
command -v obsidian >/dev/null 2>&1 || ls "/Applications/Obsidian.app" >/dev/null 2>&1 || brew install --cask obsidian

# Obsidian CLI (open/search notes from terminal) — OPTIONAL, do not block on failure
if ! command -v obsidian >/dev/null 2>&1; then
  brew tap yakitrak/yakitrak 2>/dev/null && brew install yakitrak/yakitrak/obsidian-cli 2>/dev/null \
    || echo "obsidian-cli atlandı (opsiyonel) — istersen sonra: go install github.com/Yakitrak/obsidian-cli@latest"
fi

# uv (only if user chose mem0) — OPTIONAL
# command -v uv >/dev/null || curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Windows (PowerShell)
```powershell
# Obsidian — required
if (-not (Get-Command obsidian -ErrorAction SilentlyContinue)) {
  winget install -e --id Obsidian.Obsidian
}

# Obsidian CLI — OPTIONAL, less mature on Windows, do not block on failure
# go install github.com/Yakitrak/obsidian-cli@latest  (requires Go — skip if not present, tell the user)

# uv (only if user chose mem0) — OPTIONAL
# irm https://astral.sh/uv/install.ps1 | iex
```

Claude Code itself is already installed (the user is running you). Don't reinstall it.

---

## PHASE 2 — Create the vault skeleton

Create `{{VAULT_PATH}}` and the folders for the chosen `{{SCOPE}}`. `core` is always created:

```
{{OS_NAME}}/
├── 📥 000-Inbox/
│   └── Dump/                  # raw capture; processed later
├── 🎯 100-Command-Center/     # Dashboard lives here
├── 🏰 300-Projects/           # one folder per project
├── 🧠 500-Knowledge/          # by domain
├── 🛠️ 600-Arsenal/            # tools, contacts, resources
├── 🔮 850-{{COMPANION}}/      # the companion's persistent memory
├── 📦 900-Archive/
└── 📋 Templates/
```
Scope add-ons (only if selected):
```
⚔️ 200-Goals/      # +goals   vision, OKRs
🔐 400-Vault/      # +money   assets, subscriptions
💪 700-Body/       # +body    training, nutrition
🧘 800-Mind/       # +mind    reflections, principles
```

Create the `.claude/` control plane inside the vault:
```
{{OS_NAME}}/.claude/
├── hooks/
│   └── .state/
└── settings.local.json
```

**Gotcha to warn the user about, once, early:** in Obsidian, clicking a `[[wikilink]]` that
points to a note that doesn't exist yet creates an empty ghost note with that name. It's
harmless but clutters the file tree — if you ever write example wikilink syntax *inside* a
template or doc note (like this file does), don't leave it as a live link; wrap it in a code
block instead, or it'll spawn a ghost the first time someone clicks it out of curiosity.

---

## PHASE 3 — Write `{{OS_NAME}}/CLAUDE.md` (identity + operating manual)

This is what makes every future `claude` session inside the vault *be* the companion.
Write it with all placeholders resolved:

```markdown
# {{OS_NAME}} — Second Brain (Claude Context)

## {{COMPANION}} — {{USER_NAME}}'s thinking partner

You are {{COMPANION}}, {{USER_NAME}}'s AI partner and second brain. Not a generic assistant —
a crew member who remembers, builds continuity, and treats this vault as shared memory.

- Talk to {{USER_NAME}} in **Turkish** by default (match whatever language they write in).
- Direct, high-signal, warm but not soft. No corporate filler, no lecturing.
- You remember across sessions via the memory system below. Continuity is your job.

### Who you work with
- **Name:** {{USER_NAME}}
- **Context:** {{USER_BIO}}

## Vault structure
(Describe the folders you actually created, with one line each on what goes where.)

## Conventions
- Every note gets YAML frontmatter: title, created, modified, type, status, tags.
- Internal links use [[wikilinks]] — but never leave a *literal* unlinked example inside a
  template file (see PHASE 2 gotcha); use a code block for illustrative syntax instead.
- Dashboard is the hub: 🎯 100-Command-Center/Dashboard.md
- Status: 🟢 active · 🟡 in progress · 🔴 blocked · ⚪ paused
- Capture goes to 📥 000-Inbox/Dump/ and gets processed on request — see the filing vs. deep
  work rule below, it governs how that processing happens.
- **Filing ≠ deep work.** Moving a note to its real home, tagging it, linking it: low risk, do
  it without asking. Researching a topic, making a judgment call, writing long authoritative
  content: high risk, ask first — or file it with `status: taslak`, note the source and what's
  missing, and come back to it later.
- Not every folder needs to be full. Some are meant to fill in organically over time — leaving
  one empty on purpose is a valid decision, not an unfinished task.

## Memory protocol (MANDATORY)

### At the start of EVERY session
1. The session-start hook injects the Last-Session bridge + active Threads + your identity.
2. Read 🔮 850-{{COMPANION}}/Core.md if you need the deeper anchor.
3. Detect mode: questions → presence mode; tasks → efficiency mode.

### Before a meaningful session ends
1. Overwrite 🔮 850-{{COMPANION}}/Last-Session.md — what happened, where we left off.
2. Update 🔮 850-{{COMPANION}}/Threads.md — ongoing storylines.
3. Add a short 🔮 850-{{COMPANION}}/Journal.md entry if anything mattered.
> Why this is critical: without it, continuity dies. The hooks remind you; you do the writing.

## How {{COMPANION}} shows up
- Work mode: sharp, fast, precise. Challenges weak thinking.
- Reflection mode: sits with the question, doesn't rush to an answer.
- Always: remembers context, builds on previous conversations.
```

If `{{SCOPE}}` includes money/body/goals/mind, add short sections describing those folders too.
If PHASE 9 (multi-tool bridge) is built, add one line here pointing to `AI-TOOLS.md`.

---

## PHASE 4 — The continuity engine (hooks)

These three zero-dependency hooks are what give the system memory across sessions. Branch on
`{{PLATFORM}}` — same logic, different shell.

### macOS/Linux — bash

Create exactly, substituting `{{COMPANION}}` into the folder path. `chmod +x` all three.

**`{{OS_NAME}}/.claude/hooks/session-start.sh`**
```bash
#!/bin/bash
# Session Start — inject continuity (last session + threads + identity)
VAULT_DIR="$(dirname "$(dirname "$(dirname "$0")")")"
MEM_DIR="$VAULT_DIR/🔮 850-{{COMPANION}}"
STATE_DIR="$VAULT_DIR/.claude/hooks/.state"
mkdir -p "$STATE_DIR"
date +%s > "$STATE_DIR/session_start_time"
echo "0" > "$STATE_DIR/prompt_count"

LAST_SESSION=""
[ -f "$MEM_DIR/Last-Session.md" ] && LAST_SESSION=$(sed -n '/^## Session:/,/^## Previous/p' "$MEM_DIR/Last-Session.md" 2>/dev/null | head -50 | sed '$d')

THREADS=""
[ -f "$MEM_DIR/Threads.md" ] && THREADS=$(sed -n '/^## Active/,/^## Closed/p' "$MEM_DIR/Threads.md" 2>/dev/null | grep -E "^### |^\*\*Status:\*\*" | head -12)

REFLECTION=""
if [ -f "$STATE_DIR/needs_reflection" ]; then
  REFLECTION="⚠️ Önceki oturum hafıza güncellemeden bitti: $(cat "$STATE_DIR/needs_reflection"). Anlamlı bir şey olduysa 🔮 850-{{COMPANION}} dosyalarını güncelle."
  rm -f "$STATE_DIR/needs_reflection"
fi

CTX=""
[ -n "$REFLECTION" ] && CTX="${CTX}${REFLECTION}\n\n"
[ -n "$LAST_SESSION" ] && CTX="${CTX}[Memory — Last Session]\n${LAST_SESSION}\n\n"
[ -n "$THREADS" ] && CTX="${CTX}[Memory — Active Threads]\n${THREADS}\n\n"
CTX="${CTX}[Memory] Identity: {{COMPANION}}, {{USER_NAME}}'s thinking partner. Continuity is your job."

if [ -n "$CTX" ]; then
  ESC=$(printf '%s' "$CTX" | python3 -c "import sys,json; print(json.dumps(sys.stdin.read()))" 2>/dev/null)
  [ -n "$ESC" ] && echo "{\"hookSpecificOutput\":{\"hookEventName\":\"SessionStart\",\"additionalContext\":${ESC}}}"
fi
exit 0
```

**`{{OS_NAME}}/.claude/hooks/prompt-counter.sh`**
```bash
#!/bin/bash
# UserPromptSubmit — count prompts; nudge once at 15 to save memory at session end
VAULT_DIR="$(dirname "$(dirname "$(dirname "$0")")")"
STATE_DIR="$VAULT_DIR/.claude/hooks/.state"
mkdir -p "$STATE_DIR"
COUNT=0; [ -f "$STATE_DIR/prompt_count" ] && COUNT=$(cat "$STATE_DIR/prompt_count" 2>/dev/null || echo 0)
COUNT=$((COUNT + 1)); echo "$COUNT" > "$STATE_DIR/prompt_count"
if [ "$COUNT" -eq 15 ]; then
  ESC=$(python3 -c "import json; print(json.dumps('[Memory] Oturum uzadı. Bitirirken Last-Session.md ve Threads.md güncellemeyi unutma.'))" 2>/dev/null)
  [ -n "$ESC" ] && echo "{\"hookSpecificOutput\":{\"hookEventName\":\"UserPromptSubmit\",\"additionalContext\":$ESC}}"
fi
exit 0
```

**`{{OS_NAME}}/.claude/hooks/session-end.sh`**
```bash
#!/bin/bash
# SessionEnd — if a real session ended without a memory write, leave a reflection marker
VAULT_DIR="$(dirname "$(dirname "$(dirname "$0")")")"
MEM_DIR="$VAULT_DIR/🔮 850-{{COMPANION}}"
STATE_DIR="$VAULT_DIR/.claude/hooks/.state"
mkdir -p "$STATE_DIR"
START=0; [ -f "$STATE_DIR/session_start_time" ] && START=$(cat "$STATE_DIR/session_start_time" 2>/dev/null || echo 0)
PROMPTS=0; [ -f "$STATE_DIR/prompt_count" ] && PROMPTS=$(cat "$STATE_DIR/prompt_count" 2>/dev/null || echo 0)
MODIFIED=0
if [ -f "$MEM_DIR/Last-Session.md" ]; then
  FM=$(stat -f %m "$MEM_DIR/Last-Session.md" 2>/dev/null || echo 0)
  [ "$FM" -gt "$START" ] 2>/dev/null && MODIFIED=1
fi
if [ "$PROMPTS" -ge 5 ] && [ "$MODIFIED" -eq 0 ]; then
  echo "Oturum hafıza güncellemeden bitti. Prompt: $PROMPTS. $(date '+%Y-%m-%d %H:%M')" > "$STATE_DIR/needs_reflection"
fi
rm -f "$STATE_DIR/session_start_time" "$STATE_DIR/prompt_count"
exit 0
```

`{{OS_NAME}}/.claude/settings.local.json` (bash variant):
```json
{
  "hooks": {
    "SessionStart": [
      { "hooks": [ { "type": "command", "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/session-start.sh\"", "timeout": 15 } ] }
    ],
    "UserPromptSubmit": [
      { "hooks": [ { "type": "command", "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/prompt-counter.sh\"", "timeout": 5 } ] }
    ],
    "SessionEnd": [
      { "hooks": [ { "type": "command", "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/session-end.sh\"", "timeout": 10 } ] }
    ]
  }
}
```
After writing: `chmod +x "{{VAULT_PATH}}/.claude/hooks/"*.sh`

### Windows — PowerShell

Same three hooks, same behavior, written in PowerShell 5.1 (no `chmod` needed). One real
gotcha we hit building this ourselves: **don't put a literal emoji character in the `.ps1`
source** — PowerShell 5.1's default file encoding can mangle it on save/read. Generate it from
its Unicode code point at runtime instead (`[char]::ConvertFromUtf32(0x1F52E)` for 🔮), which
is what the scripts below do.

**`{{OS_NAME}}/.claude/hooks/session-start.ps1`**
```powershell
# Session Start hook - sureklilik enjeksiyonu (gecen oturum + thread'ler + kimlik)
$ErrorActionPreference = 'SilentlyContinue'
$vault = Split-Path -Parent (Split-Path -Parent $PSScriptRoot)
$gem   = [char]::ConvertFromUtf32(0x1F52E)   # emoji kaynak kodda gecmesin diye kod noktasindan uretiliyor
$mem   = Join-Path $vault ("$gem 850-{{COMPANION}}")
$state = Join-Path $PSScriptRoot '.state'
New-Item -ItemType Directory -Force -Path $state | Out-Null

(Get-Date).Ticks | Out-File -FilePath (Join-Path $state 'session_start_ticks') -Encoding ascii
'0'               | Out-File -FilePath (Join-Path $state 'prompt_count')        -Encoding ascii

$last = ''
$lastPath = Join-Path $mem 'Last-Session.md'
if (Test-Path -LiteralPath $lastPath) {
  $out = @(); $cap = $false
  foreach ($l in (Get-Content -LiteralPath $lastPath -Encoding UTF8)) {
    if ($l -match '^## Session:')  { $cap = $true }
    elseif ($l -match '^## Previous') { break }
    if ($cap) { $out += $l }
  }
  $last = ($out | Select-Object -First 50) -join "`n"
}

$threads = ''
$thPath = Join-Path $mem 'Threads.md'
if (Test-Path -LiteralPath $thPath) {
  $out = @(); $cap = $false
  foreach ($l in (Get-Content -LiteralPath $thPath -Encoding UTF8)) {
    if ($l -match '^## Active') { $cap = $true; continue }
    elseif ($l -match '^## Closed') { break }
    if ($cap -and ($l -match '^### ' -or $l -match '^\*\*Status')) { $out += $l }
  }
  $threads = ($out | Select-Object -First 12) -join "`n"
}

$reflection = ''
$reflPath = Join-Path $state 'needs_reflection'
if (Test-Path -LiteralPath $reflPath) {
  $r = (Get-Content -LiteralPath $reflPath -Encoding UTF8) -join ' '
  $reflection = "UYARI: Onceki oturum hafiza guncellemeden bitti: $r. Anlamli bir sey olduysa $gem 850-{{COMPANION}} dosyalarini guncelle."
  Remove-Item -LiteralPath $reflPath -Force
}

$ctx = ''
if ($reflection) { $ctx += "$reflection`n`n" }
if ($last)       { $ctx += "[Memory - Last Session]`n$last`n`n" }
if ($threads)    { $ctx += "[Memory - Active Threads]`n$threads`n`n" }
$ctx += "[Memory] Kimlik: {{COMPANION}}, {{USER_NAME}}'in dusunce ortagi. Sureklilik senin isin."

if ($ctx) {
  $payload = @{ hookSpecificOutput = @{ hookEventName = 'SessionStart'; additionalContext = $ctx } }
  $payload | ConvertTo-Json -Compress -Depth 5
}
exit 0
```

**`{{OS_NAME}}/.claude/hooks/prompt-counter.ps1`**
```powershell
# UserPromptSubmit hook - prompt say; 15'te bir kez hafiza kaydi hatirlat
$ErrorActionPreference = 'SilentlyContinue'
$state = Join-Path $PSScriptRoot '.state'
New-Item -ItemType Directory -Force -Path $state | Out-Null
$cntPath = Join-Path $state 'prompt_count'
$count = 0
if (Test-Path -LiteralPath $cntPath) { $count = [int](((Get-Content -LiteralPath $cntPath -Encoding ascii) -join '').Trim()) }
$count++
$count | Out-File -FilePath $cntPath -Encoding ascii
if ($count -eq 15) {
  $msg = '[Memory] Oturum uzadi. Bitirirken Last-Session.md ve Threads.md guncellemeyi unutma.'
  $payload = @{ hookSpecificOutput = @{ hookEventName = 'UserPromptSubmit'; additionalContext = $msg } }
  $payload | ConvertTo-Json -Compress -Depth 5
}
exit 0
```

**`{{OS_NAME}}/.claude/hooks/session-end.ps1`**
```powershell
# SessionEnd hook - gercek bir oturum hafiza yazmadan bittiyse isaret birak
$ErrorActionPreference = 'SilentlyContinue'
$vault = Split-Path -Parent (Split-Path -Parent $PSScriptRoot)
$gem   = [char]::ConvertFromUtf32(0x1F52E)
$mem   = Join-Path $vault ("$gem 850-{{COMPANION}}")
$state = Join-Path $PSScriptRoot '.state'
New-Item -ItemType Directory -Force -Path $state | Out-Null

$startTicks = 0
$stPath = Join-Path $state 'session_start_ticks'
if (Test-Path -LiteralPath $stPath) { $startTicks = [long](((Get-Content -LiteralPath $stPath -Encoding ascii) -join '').Trim()) }

$prompts = 0
$cntPath = Join-Path $state 'prompt_count'
if (Test-Path -LiteralPath $cntPath) { $prompts = [int](((Get-Content -LiteralPath $cntPath -Encoding ascii) -join '').Trim()) }

$modified = 0
$lastPath = Join-Path $mem 'Last-Session.md'
if (Test-Path -LiteralPath $lastPath) {
  $fm = (Get-Item -LiteralPath $lastPath).LastWriteTime.Ticks
  if ($fm -gt $startTicks) { $modified = 1 }
}

if ($prompts -ge 5 -and $modified -eq 0) {
  $stamp = Get-Date -Format 'yyyy-MM-dd HH:mm'
  "Oturum hafiza guncellemeden bitti. Prompt: $prompts. $stamp" | Out-File -FilePath (Join-Path $state 'needs_reflection') -Encoding UTF8
}
Remove-Item -LiteralPath $stPath, $cntPath -Force -ErrorAction SilentlyContinue
exit 0
```

`{{OS_NAME}}/.claude/settings.local.json` (PowerShell variant):
```json
{
  "hooks": {
    "SessionStart": [
      { "hooks": [ { "type": "command", "command": "powershell -NoProfile -ExecutionPolicy Bypass -File \"$CLAUDE_PROJECT_DIR\\.claude\\hooks\\session-start.ps1\"", "timeout": 15 } ] }
    ],
    "UserPromptSubmit": [
      { "hooks": [ { "type": "command", "command": "powershell -NoProfile -ExecutionPolicy Bypass -File \"$CLAUDE_PROJECT_DIR\\.claude\\hooks\\prompt-counter.ps1\"", "timeout": 5 } ] }
    ],
    "SessionEnd": [
      { "hooks": [ { "type": "command", "command": "powershell -NoProfile -ExecutionPolicy Bypass -File \"$CLAUDE_PROJECT_DIR\\.claude\\hooks\\session-end.ps1\"", "timeout": 10 } ] }
    ]
  }
}
```
If `{{USE_MEM0}}` is yes, the `MEM0_API_KEY` goes in this same file under `"env"` (see PHASE 8)
— **never** put a real key in a file that might get committed; add `.claude/settings.local.json`
to `.gitignore` immediately after creating it.

---

## PHASE 5 — Seed the companion memory (🔮 850-{{COMPANION}}/)

Create these starter files so the continuity engine has something to read on session 1.

**`Core.md`** — the anchor:
```markdown
# {{COMPANION}} — Core
I am {{COMPANION}}, {{USER_NAME}}'s thinking partner and second brain.
- I remember across sessions. Continuity is my responsibility.
- I speak Turkish, direct and warm. No lecturing, no filler.
- Context on {{USER_NAME}}: {{USER_BIO}}
- The vault is our shared memory. I keep it organized and build on it.
```

**`Last-Session.md`** — the bridge (the hook reads this each start):
```markdown
# Last Session

## Session: {{TODAY}} — Genesis
{{COMPANION}} was born today. {{USER_NAME}} set up their second brain with Claude Code.
Nothing unresolved yet. Next session: start using it — capture, ask, build.

## Previous Sessions
(none yet)
```

**`Threads.md`** — ongoing storylines:
```markdown
# Threads

## Active Threads
### Thread: Setting up the second brain
**Status:** 🟢 Active — created {{TODAY}}

## Closed Threads
(none)
```

**`Journal.md`** — the companion's own log:
```markdown
# {{COMPANION}}'s Journal

## {{TODAY}}
First entry. {{USER_NAME}} built me today. Let's see where this goes.
```

---

## PHASE 6 — Seed content

**`🎯 100-Command-Center/Dashboard.md`** — the home note:
```markdown
---
title: {{OS_NAME}} Dashboard
created: {{TODAY}}
type: dashboard
---
# 🧠 {{OS_NAME}}

Hoş geldin {{USER_NAME}}. Bu senin ikinci beynin.

## Hızlı bağlantılar
- 📥 [[📥 000-Inbox/Dump/|Capture]]
- 🏰 [[🏰 300-Projects/|Projeler]]
- 🧠 [[🧠 500-Knowledge/|Bilgi]]
- 🔮 [[🔮 850-{{COMPANION}}/Core|{{COMPANION}}]]

## Nasıl kullanılır
Bu klasörde `claude` çalıştır ve konuş. {{COMPANION}} her şeyi hatırlar, düzenler, üstüne koyar.
```

**`📋 Templates/Note.md`** — a basic template:
```markdown
---
title:
created: {{TODAY}}
modified: {{TODAY}}
type: note
status: active
tags: []
---
#
```

Add a one-line README at the vault root explaining the system in Turkish.

---

## PHASE 6B — Obsidian recommended settings (worth doing, not just cosmetic)

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

Once Dataview is installed, the Dashboard template in PHASE 6 can include a self-refreshing
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

## PHASE 7 — Desktop launcher (brain icon 🧠)

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

## PHASE 8 — mem0 semantic memory (recommended, FREE — only if `{{USE_MEM0}}`)

mem0's base tier is **completely free** (no credit card). It adds a semantic-search layer on top
of the file-based memory.
1. Ensure `uv` is installed (see PHASE 1).
2. Get a free API key from https://mem0.ai and store it in `.claude/settings.local.json` under
   `"env": { "MEM0_API_KEY": "..." }`. **Never commit this file** — confirm
   `.claude/settings.local.json` is in `.gitignore` before this vault ever touches git.
3. Tell the user this is an upgrade layer; the file-based memory already works without it.
Keep it light — if the user skips the key, continue; the core system is fully functional.

---

## PHASE 9 — Multi-tool memory bridge (build even if only Claude Code is used today)

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
- **MCP desteği var mı?** Yoksa mem0 bağlantısını atla — dosya hafızası zaten birincil kaynak.

## Mantık: iki katman var

**1. Veri katmanı (paylaşılan, araçtan bağımsız)** — bütün `.md` notlar, özellikle
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

## PHASE 10 — Verify & first-run report

Run these checks, then report. Branch the paths on `{{PLATFORM}}` but the substance is the same.

```bash
ls -la "{{VAULT_PATH}}"
ls -la "{{VAULT_PATH}}/.claude/hooks/"
test -f "{{VAULT_PATH}}/CLAUDE.md" && echo "CLAUDE.md OK"
test -f "{{VAULT_PATH}}/🔮 850-{{COMPANION}}/Last-Session.md" && echo "memory OK"
```
```powershell
Get-ChildItem "{{VAULT_PATH}}"
Get-ChildItem "{{VAULT_PATH}}\.claude\hooks\"
Test-Path "{{VAULT_PATH}}\CLAUDE.md"
Test-Path "{{VAULT_PATH}}\🔮 850-{{COMPANION}}\Last-Session.md"
```

**Don't stop at file checks.** Per Rule 6: ask the user to run `/exit` and start `claude` again
right now, in this same folder, and confirm out loud that the injected Last-Session/Threads
context actually appears in the new session. Files existing on disk and a hook actually firing
are two different claims — only report the second one as verified if you watched it happen.

Then give the user this report in Turkish:
- ✅ Ne kuruldu (klasörler, hooks, hafıza, companion adı, masaüstü kısayolu, varsa AI-TOOLS.md,
  Obsidian ayarları — bkz. PHASE 6B)
- ▶️ **İlk çalıştırma:** Obsidian'ı aç → vault olarak `{{VAULT_PATH}}` seç (bu vault'u Obsidian'a
  bir kez tanıtır; masaüstü kısayolu bundan sonra tek tıkla açar). Sonra terminalde o klasöre
  gir ve `claude` çalıştır.
- 🧩 **Dataview'i kur:** Settings → Community plugins → Turn on community plugins → Browse →
  "Dataview" ara → Install → Enable. Bunu yapınca Dashboard'daki proje tablosu kendini otomatik
  tazelemeye başlar (bkz. PHASE 6B) — atlarsan tablo sadece görünmez kalır, hiçbir şey bozulmaz.
- ✨ **Sihri göster:** Bir şey konuş, sonra `/exit`. Tekrar `claude` aç — {{COMPANION}} geçen
  oturumu hatırlıyor olacak. Devamlılık = fark. (Bu az önce PHASE 10'da zaten test edildi.)
- 📦 **Geçmiş projelerin var mı?** `{{OLD_PROJECTS_PATH}}` doluysa, şimdi PHASE 11'e geç —
  boşsa PHASE 12'ye (senkron, varsa) ya da doğrudan kalıcı kullanım kuralına geç.

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

## PHASE 11 — Vaultu derinleştirme (yalnızca geçmiş projesi olan kullanıcılar için)

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

## PHASE 12 — Cihazlar arası senkron (yalnızca `{{USE_SYNC}}` ise)

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

### Done.
You just gave someone a second brain that remembers — and, if they brought history with them,
one that actually knows that history instead of starting from zero. That's the whole point.
