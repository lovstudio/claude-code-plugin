# Changelog

## 0.4.0

- **statusline**: ship `vibe-genius.sh` as a managed script (`scripts/statusline/vibe-genius.sh`), migrated from the now-archived `claude-code-manager` repo
- **statusline**: daily token counter with incremental per-session caching in `~/.claude/.daily_tokens` — O(delta) per tick, multi-session aggregation comes for free
- **statusline**: session title suffix — prefers the `/compact` summary from the transcript, falls back to the first user prompt
- **statusline**: display reordered to `cwd (branch) │ model (provider) │ cost / tokens │ version │ title`; cost/tokens bolded, title bold-white
- **/lovstudio:better:statusline**: v2.0.0 — default mode now installs the script + wires `settings.json`; legacy inline-string mode preserved behind the `inline` argument

## 0.3.1

- **hooks**: `SessionEnd` no longer copies the resume command on `/clear` or interactive `/resume` — only fires on real exits (`prompt_input_exit` / `logout` / `other`)

## 0.3.0

- **hooks**: `SessionEnd` now copies `cd <cwd> && claude --resume <session_id>` to the clipboard on exit (macOS `pbcopy`) so you can paste-resume the session
- **skills**: submodule pointer bumped
- **versioning**: switched to 0.x semantic versioning (previous 1.0.0 / 1.1.0 / 1.2.0 tags rewritten)

## 0.2.0

- **commands**: all top-level commands moved under `commands/lovstudio/` so every command gets the `/lovstudio:` namespace prefix (e.g. `/code-reviewer` → `/lovstudio:code-reviewer`)
- **marketplace**: renamed marketplace to `lovstudio-cc-plugins`; canonical repo moved to `lovstudio/cc-plugins`
- **plugin.json**: fixed stale `repository` URL

## 0.1.1

- **png2svg**: PNG 转高质量 SVG（ImageMagick + vtracer + svgo）

## 0.1.0

- Initial release
- **image-gen**: Generate images using Gemini via ZenMux API
- **project-port**: Generate stable, hash-based port numbers for projects
- **Hooks**: Desktop notifications with Lovnotifier integration
