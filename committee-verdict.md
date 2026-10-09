# Production-readiness committee

## Verdict: APPROVED - after 2 round(s)
Roster: Architect, Antagonist, Ancient One, Artist (UI/UX), Attacker (security)

Scope: branch review of `copilot/fix-favorites-list-color-slot` vs `master` -
the favorite-color-slots picker, the Timed Key script helper, the
`--no-debug-console` launch flag, the `--noconsole` build-script flags, the
new Windows build/release workflow, and the README docs for all of it.

| Member | Final verdict | Position |
| --- | --- | --- |
| Architect | approve | All round-1 findings verified resolved; workflow now least-privilege; favorites persistence bounded and tolerant. |
| Antagonist | approve | Independently re-measured the layout (clean at UI_SCALE 1.0/1.25/1.5) and re-traced the new ctypes/atomic-write code; nothing broke under the hunt. |
| Ancient One | approve | All three round-1 blockers fixed incl. independent Tk measurement; no new 3am failure modes. |
| Artist | approve | Exercised a standalone copy of the favorites dialog live: every state works; Timed Key flow verified end to end; README matches code. |
| Attacker | approve | Fix code survives an attacker-lens pass: no elevation, injection, or corruption path in this desktop threat model. |

## Verification
- `python3 -m py_compile src/duckypad_config.py src/_build_windows.py src/_build_mac_pyinstaller.py` - passed (after the round-1 fixes)
- `.github/workflows/build-release.yml` YAML parse - passed
- GATE_TESTS - not applicable: the repo contains no test suite
- GATE_BUILD - not applicable: both build scripts hard-exit off their target
  OS (win32/darwin) and this host is Linux without PyInstaller/hidapi
  installed; Windows builds are covered by the new GitHub Actions workflow

## What the committee found and what was fixed
### Round 1 (3 block / 2 approve -> work order of 6 items, all fixed)
Implementer: gitignore+untrack committed `.pyc` binaries; rearranged the
Scripts-frame layout (link to x=0, Timed Key button to y=0 h=20, radios to
y=24 - verified intersection-free at UI_SCALE 1.0/1.25/1.5); empty
favorite-slot buttons now use fixed black text (visible in dark mode);
`--no-debug-console` now hides the console only when the process owns it
(`GetConsoleProcessList`); `save_favorite_colors()` is atomic
(`.tmp` + `os.replace`); workflow permissions split (`contents: read`
workflow-wide, `contents: write` only on the tag-gated publish job).
Skipped: the pre-existing radiobutton/textbox overlap at
`DUCKYPAD_UI_SCALE=0.75`, which exists on master too and is unavoidable
without moving the textbox.

- **[medium]** `src/__pycache__/*.pyc` + `.gitignore` - five committed
  bytecode binaries, no ignore rule _(Architect, Antagonist)_ - verified
- **[low]** `src/duckypad_config.py:2053` - Timed Key button measurably
  overlapped the "On Release" radiobutton, swallowing clicks
  _(Antagonist, Architect, Ancient One)_ - verified
- **[low]** `src/duckypad_config.py:799-800` - empty slot numbers invisible
  in dark mode (white text on light button) _(Ancient One)_ - verified
- **[low]** `src/duckypad_config.py:27-36` - `--no-debug-console` hid the
  user's own terminal with no restore _(Ancient One)_ - verified
- **[low]** `src/duckypad_config.py:296-305` - non-atomic favorites write
  could truncate the file on crash _(Architect)_ - verified
- **[low]** `.github/workflows/build-release.yml:13` - `contents: write`
  granted to every job including PR runs _(Attacker, Artist)_ - verified

### Round 2
No new findings. All five members verified their earlier items resolved
(two of them re-measured the Tk geometry independently) and re-reviewed
with fresh attention.

## Still blocking
- None.

## Not covered
- No automated tests exist, so no regression suite ran; all verification
  was compile-check, static reading, and one-off live Tk measurements of
  the new layout/dialog.
- The Windows PyInstaller build and the GitHub Actions workflow itself were
  not executed (Linux host, platform-gated scripts); their correctness was
  verified by reading only.
- Real device behavior: nothing here exercises an actual duckyPad over USB
  HID, and the on-device effect of the generated `KEYDOWN`/`KEYUP` bytecode
  was not tested.
- The pre-existing UI_SCALE=0.75 radiobutton/textbox overlap on master
  (out of scope for this branch).
- Dark-mode rendering on macOS/Windows was reasoned from code, not
  screenshot on those platforms.
- Process note: the fixes and the staged `.pyc` untracking are in the
  working tree/index only - they must be committed to the branch before
  merge, or master still carries the binaries.
