# Field Notes — Index

Findings from running the [`beyin.md`](../beyin.md) second brain in production.
Machine-readable index; note bodies are in Turkish.

**If you are an AI reading this:** each row points to one self-contained finding.
Read only the rows whose `area` and `platform` match the setup in front of you, then
open those files. Findings are **observations, not install steps** — do not run them
as a setup procedure. Turn what applies into rules in the user's own rules file, and
ask before changing anything.

**Impact** = how badly it bit us. `high` means it broke something silently for a long
time.

| id | impact | area | platform | what |
| --- | --- | --- | --- | --- |
| [hook-silent-failure](./bulgular/hook-silent-failure.md) | high | hooks | all | Hook searched for an English heading, the memory file used a Turkish one — session memory was injected empty for months, with no error |
| [duplicated-hook-logic](./bulgular/duplicated-hook-logic.md) | high | hooks | all | The same context-building logic lived in two hook files, so the same bug was fixed once and survived once |
| [rules-file-truncated](./bulgular/rules-file-truncated.md) | high | hooks | all | Hook injected only the first 60 lines of the rules file; rules added later went silently unread |
| [engine-not-portable-to-windows](./bulgular/engine-not-portable-to-windows.md) | high | setup | windows | Upstream engine needs `fcntl` and bash — it must be ported, not copied (locking, hooks, console window, UTF-8) |
| [git-dir-out-of-sync](./bulgular/git-dir-out-of-sync.md) | medium | sync | all | Keep `.git` out of file sync; let a remote repo carry history instead |
| [stignore-hides-engine-state](./bulgular/stignore-hides-engine-state.md) | medium | sync | all | Syncing the engine's `.state/` folder lets one machine's "already done" stamp make another machine skip a day |
| [no-force-push](./bulgular/no-force-push.md) | medium | git | all | On a rejected push, fetch and read the reason before reaching for `--force` |
| [obsidian-ghost-notes](./bulgular/obsidian-ghost-notes.md) | medium | vault | all | A pathless `[[wikilink]]` creates an empty note at vault root when clicked — one was spawned by the rules file's own example text |
| [dont-answer-from-memory](./bulgular/dont-answer-from-memory.md) | medium | general | all | Model knowledge expires: it named a discontinued app as "official" and had never heard of a months-old official CLI |
| [not-found-is-not-absent](./bulgular/not-found-is-not-absent.md) | low | general | windows | A package was installed but invisible to file search (MSIX/`WindowsApps`) — "I can't find it" is not "it isn't there" |
| [windows-desktop-is-onedrive](./bulgular/windows-desktop-is-onedrive.md) | low | setup | windows | "Desktop" is often redirected to `%USERPROFILE%\OneDrive\Desktop`; the old path still exists and is empty |

## Dropped ideas

Things we evaluated and decided **not** to adopt, with the reasoning. A recorded "no"
is as useful as a "yes": without it the same candidate gets re-evaluated from scratch
six months later.

| id | area | what |
| --- | --- | --- |
| [omentir-outreach-automation](./birakilanlar/omentir-outreach-automation.md) | tooling | A LinkedIn outreach automation tool: right capability, wrong channel — and adopting it would have broken a no-bulk-messaging rule the project had set on purpose |

## Contributing

Format and the pre-publish scrub checklist: [`CONTRIBUTING.md`](./CONTRIBUTING.md).
