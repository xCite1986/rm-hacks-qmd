# rm-hacks-qmd v0.0.11-pre4

**EXPERIMENTAL** - Ported to 3.24.0.149 (untested!)

This is an experimental port attempting to support firmware 3.24.0.149.
The QMD files are identical to 0.0.11-pre3, only the VERSION directive has been updated.

## Compatibility

| Firmware Version | Status |
|-----------------|--------|
| 3.24.0.149 | ⚠️ Experimental (untested) |
| 3.22.4.2 | ✅ Should work (inherited from pre3) |
| 3.22.0.65 | ✅ Should work (inherited from pre3) |
| 3.22.0.64 | ✅ Should work (inherited from pre3) |

## Requirements

- xovi extensions >= v14-20092025
- qt-resource-rebuilder with rebuilt hashtab for your firmware

## Installation

1. Install xovi and qt-resource-rebuilder
2. Run `xovi/rebuild_hashtable` to build the hashtab for 3.24.0.149
3. Copy `zz_rmhacks.qmd` and `rmHacks/` to `/home/root/xovi/exthome/qt-resource-rebuilder/`
4. Run `xovi/debug` to test
5. If working, run `xovi/start` for normal operation

## Troubleshooting

If errors occur during `xovi/debug`, it means the QML structure in 3.24.0.149 
has changed compared to 3.22.x. In that case:
- Check the error messages for which QMD file fails
- The hash references may need to be updated for the new firmware

## Features (inherited from pre3)

- ✅ Split document view
- ✅ Bookmarks
- ✅ Clock in document view
- ✅ Battery/WiFi indicators
- ✅ Gesture controls (toolbar toggle, eraser, pen switch, etc.)
- ✅ More stroke sizes
- ✅ More colors
- ✅ Auto new page
- ✅ And more...

---
Ported by Alex, December 2024
