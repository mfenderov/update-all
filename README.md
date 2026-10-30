# update-all

One command to update everything. Runs on macOS and Linux.

## Install

```sh
curl -fsSL -H "Authorization: Bearer $(gh auth token)" \
  https://raw.githubusercontent.com/mfenderov/update-all/main/update-all \
  -o ~/.local/bin/update-all
chmod +x ~/.local/bin/update-all
```

(requires `gh` authenticated on that machine; the repo is private)

Optional wrapper for `~/.zshrc` (keeps the function name stable if the file moves):

```sh
update-all() {
  "$HOME/.local/bin/update-all" "$@"
}
```

Pin to a version instead of `main` by replacing `main` with a tag, e.g. `v1.0.0`.

## Usage

```sh
update-all [-h|--help]
UPD_ALL_OS=1 update-all                 # include the machine itself (needs sudo)
UPD_ALL_RESTART_SERVICE=1 update-all   # allow opencode service restart inside an opencode terminal
```

## What it updates

brew, oh-my-zsh, npm globals (+npm itself), bun globals (+bun itself), rustup,
opencode (+plugins, +service restart), claude (+marketplace, +plugins),
pi, copilot, crush providers, uv tools, gh extensions, pipx, cargo packages.

The machine itself is opt-in via `UPD_ALL_OS=1` (needs sudo):

- macOS: `softwareupdate -i -a` (macOS, Safari, Xcode CLT, firmware) + `mas upgrade` (Xcode.app and App Store apps)
- Linux: `apt full-upgrade` + `do-release-upgrade` (stays interactive on purpose)

Every non-interactive section runs with stdin closed. Anything that wants
confirmation fails fast instead of hanging a daemon/cron/launchd run.
Missing tools are reported as skipped, not as failures.

## macOS notes

- Apple Silicon brew (`/opt/homebrew/bin`) is added to `PATH` when missing, so the script also works from launchd/cron/ssh. Intel (`/usr/local/bin`) fallback included.
- `xcode-select --install` is deliberately absent: it pops a GUI dialog and would hang a headless run.
