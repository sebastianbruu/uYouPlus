# uYouPlus

Jailed (sideloaded) YouTube tweak bundle built with Theos. Builds only in CI (macOS); there is no local build on Windows.

## Build
- CI: `.github/workflows/buildapp.yml` (workflow_dispatch only). Trigger:
  `gh workflow run buildapp.yml -R sebastianbruu/uYouPlus --ref main -f decrypted_youtube_url=<direct .ipa URL>`
- Failed log: `gh run view <id> -R sebastianbruu/uYouPlus --log-failed`
- `gh` is at `C:\Program Files\GitHub CLI\gh.exe` and may not be on PATH in older terminals.

## Gotchas
- The IPA URL must be a direct download (e.g. a GitHub release asset). LimeWire-style share pages return HTML and `unzip` fails.
- Tweaks are submodules under `Tweaks/`. Bump `Tweaks/YouTubeHeader` together with tweaks that use it, or builds fail with "no visible @interface ... declares the selector".
- The main `Makefile` builds tweaks as SUBPROJECTS, so `THEOS_PROJECT_DIR` is the repo root inside every sub-make. Tweak Makefiles that use `$(THEOS_PROJECT_DIR)/...` resolve against the root; CI symlinks `vendor` -> `Tweaks/YTUHD/vendor` for this.
- YTUHD needs libvpx and dav1d static libs built first (meson, ninja); the "Build YTUHD libraries" CI step does it.
- `export SIDELOAD = 1` in the main `Makefile` keeps YTUHD from linking libundirect, which a sideloaded app doesn't have.
- The workflow edits `BUNDLE_ID` / `DISPLAY_NAME` in the `Makefile` by name with `sed`; keep those lines as `NAME = value`.
- macOS arm64 runners often queue for minutes or retry startup; that's GitHub capacity, not a repo bug.
