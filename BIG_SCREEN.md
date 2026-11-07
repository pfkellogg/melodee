# Melodee + FM-1 Big Screen

[Melodee](https://github.com/keremimo/melodee) by keremimo (a modified version of Leo Kuroshita's
[Felucca](https://github.com/hugelton/Felucca)), with one addition: while an app asks for it, the FM-1 sends its screen,
the sound playing and the BPM over its USB cable, so [FM-1 Mirror](https://github.com/pfkellogg/fm1-mirror) and
[FM-1 Mirror + Spyglass](https://github.com/pfkellogg/fm1-mirror-spyglass-android) show it big, live.
For players who can't read the FM-1's 1.54-inch screen.

This is an **unofficial** build, not a Melodee release, and keremimo has nothing to do with it. Please report problems
with it to FM-1 Mirror, not to Melodee. Everything else is **Melodee 1.6.1** (tag `v1.6.1`, commit `165d177`) as published.
The addition is `firmware/src/melodee_mirror.c` and `firmware/src/screen_mirror.c`, with small hooks in `lcd.c`, `main.c`,
`melodee.c` and `usb.c`. With nobody asking, it does nothing. USB only: Melodee has Bluetooth MIDI, but this build doesn't
send the screen over it.

Tested on one real FM-1 (2026-11-07), installed over Melodee 1.6.1: with FM-1 Mirror on a Mac and FM-1 Mirror + Spyglass
on an Android tablet, the screen followed the knobs and pages, a USB keyboard played it through the app, and the sound was
normal. Not tested for long sessions, or over any firmware but Melodee 1.6.1.

**What's different from official Melodee:**

- About 7 KB less memory for audio (79,680 bytes of audio resources, not 86,720; Melodee's own build checks pass).
- It was built with JieLi's Windows toolchain under Wine, not Melodee's release workflow, so it has **no build
  attestation** and is not byte-for-byte a Melodee build.
- It still calls itself Melodee 1.6.1, and it doesn't update itself.

## Installing it

Install at your own risk, and back up your sounds and projects first with Melodee's web editor.

1. Your FM-1 should be running **Melodee 1.6.1**. From another firmware, install official Melodee first, the way
   [Melodee's README](https://github.com/keremimo/melodee#readme) says. Don't install one custom firmware straight over
   another unless its instructions say that's safe. We went from SLOOP to M-VAVE's V15 (with Clipper's
   [Return to the official firmware](https://isod89.github.io/clipper-fm1/webapp/installer/)), then to Melodee.
2. Charge the FM-1, plug it straight into the computer (no hub), and close every app that uses it.
3. Download **Melodee-1.6.1-BigScreen.fwsc** and **Melodee-1.6.1-BigScreen-res.bin** from this fork's
   [Releases](https://github.com/pfkellogg/melodee/releases), into the same folder.
4. With this repository and Python 3 (`pip3 install mido python-rtmidi`), from its folder:

   ```
   python3 tools/fm1_install.py Melodee-1.6.1-BigScreen.fwsc --yes
   ```

5. If the computer doesn't see the FM-1 after it restarts, unplug the USB cable and plug it back in.

A Mac can remember an older name for the FM-1's port, so it may still show another firmware's name. That's harmless.

**Back:** Melodee's own [web installer](https://keremimo.github.io/melodee/) should put plain Melodee back (not tested yet).

## How it works in Melodee

It's the same mirror as the other Big Screen builds, with two changes. Melodee has only about 1 KB of ordinary RAM left, so
its buffers live in the `.pool` section. And Melodee sends only the parts of the screen that changed, so when a viewer
connects the mirror calls `gfx_forget()`, or the viewer would get little more than the outlines.

To build it without Docker on a Mac, set `JIELI_WINE` and `JIELI_WIN_BIN`: `tools/build.py` then runs JieLi's Windows
toolchain (the same clang 4.0.1 for pi32v2).

Questions: [FM-1 Mirror's issues](https://github.com/pfkellogg/fm1-mirror/issues).

GPL-3.0, like Melodee: see [LICENSE](LICENSE). `melodee_mirror.c` and `screen_mirror.*` are MIT, which GPL-3.0 allows.
