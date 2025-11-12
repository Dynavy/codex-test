# Repository Guidelines

## Project Structure & Module Organization
The root currently contains `README.md`, which documents the Codex-on-WSL workflow. Keep the root lean: place new how-to guides or deep dives under `docs/` (create the folder if absent), helper scripts under `scripts/`, and reference artifacts (e.g., VS Code `settings.json` samples) under `config/`. When expanding the repo, mirror this structure in your prose so contributors know exactly where to look; call out paths explicitly (`docs/wsl/troubleshooting.md`) instead of relying on context.

## Build, Test, and Development Commands
- `markdownlint README.md AGENTS.md docs/**/*.md` — lint Markdown before committing so headings, code fences, and tables remain consistent.
- `wsl --list --verbose` — confirm the distro ordering you describe in documentation actually matches a clean workstation.
- `nvm install 22 && node -v` — rerun the Node setup path in WSL to ensure step-by-step accuracy; note deviations in the doc.
There is no compiled artifact in this repo today, so the focus is on verifying the reproducibility of the documented workflows.

## Coding Style & Naming Conventions
Write in concise, instructional Spanish (matching `README.md`), but include English command names verbatim. Use sentence-case headings (`## Configuración inicial`), fenced code blocks with language tags (` ```powershell `), and bullet lists for procedures. Inline paths, commands, and keys belong in backticks. For future scripts, prefer lowercase-kebab filenames (`scripts/bootstrap-wsl.sh`) and two-space indentation for readability inside shell or JSON snippets.

## Testing Guidelines
Every command sequence you document must be executed in a fresh WSL session before submission; capture notable outputs and mention prerequisite tools. When adding walkthrough files, name them `{topic}-guide.md` and include a short "Verificación" subsection that states how to confirm success. Aim for full coverage of described scenarios: if you update the WSL default-distro instructions, also verify `wsl --set-default Ubuntu` and include any error codes encountered.

## Commit & Pull Request Guidelines
The repository has no historical commits yet, so set the tone with Conventional Commits (`docs: describe WSL defaults`, `chore: add markdownlint hints`). Keep commit scopes small—one conceptual change per commit. Pull requests should summarize the scenario validated, link to any upstream issues, and embed screenshots or terminal excerpts when they clarify environment-specific steps. Request review from another agent when you alter installation flows or recommend new tooling.

## Security & Configuration Tips
Never check in personal `settings.json` files or tokens; provide redacted samples instead. When documenting VS Code changes, show only the keys contributors must add (e.g., `chatgpt.runCodexInWindowsSubsystemForLinux`). Remind readers to rotate access tokens after testing automation scripts, and prefer referencing official download links over re-hosting binaries inside this repo.
