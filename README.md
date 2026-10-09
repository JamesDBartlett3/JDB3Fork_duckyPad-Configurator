# duckyPad Configurator

This is the software that configures [duckyPad Macropads](duckypad.com)!

### How to Use

* [Click me to download the latest version](https://github.com/duckyPad/duckyPad-Configurator/releases/latest)

* [Click me for full instructions](https://dekunukem.github.io/duckyPad-Pro/doc/getting_started.html)

### What's New In This Fork

* Color picking now supports **favorite slots** that can be selected independently from the current working color.
* The **Save to Slot** action stores the current color in the selected favorite slot without first switching the active color to that slot.
* The Scripts panel now includes a **Timed Key** helper that inserts a `DEFAULTDELAY` + `KEYDOWN` + `KEYUP` press/release block for a selected key.
* Keys can be **copied and pasted** with a right-click menu, either within a profile or into a different profile. Paste comes in three flavors: the whole key, **code only** (both scripts plus the abort/repeat flags, on-press script only, or on-release script only), or **style only** (text and color, text only, or color only). All of them work on empty slots — whole-key paste fills the slot, while code/style-only create a placeholder "New Key" holding just what you pasted. On occupied slots, they merge into the existing key.
* The app can be launched without the debug console by passing `--no-debug-console`.

### Windows Test Builds

* Manual test builds are published as a GitHub Actions artifact named `duckypad-config-windows`.
* Open the repository's **Actions** tab, run **Build and release Windows app**, then download the latest `duckypad-config-windows` artifact from that run.
* GitHub **Releases** are only created for pushed tags matching `v*`.

### Standalone Build Notes

* Windows standalone builds are created from `src/_build_windows.py`
* macOS standalone builds are created from `src/_build_mac_pyinstaller.py`
* To build a Windows package without the console window, use the workflow above or run the Windows build script with `--noconsole`

### Feedbacks

* [Open an issue](https://github.com/duckyPad/duckyPad-Configurator/issues)
* Ask in [official duckyPad discord](https://discord.gg/4sJCBx5)
* Email dekuNukem`@`gmail.com!