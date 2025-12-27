# rm-hacks 3.24 Porting Notes

## Hash Format Discovery

**CRITICAL:** The hashtab binary format uses **BIG-ENDIAN** for both hash (8 bytes) and length (4 bytes)!

Previous incorrect assumption was little-endian hash + big-endian length.

### Hashtab Binary Format
```
Position 0-71:   Header ("Hashtab file for QMLDIFF..." + version)
Position 72+:    Entries in format:
                 [8 bytes hash BE][4 bytes length BE][string bytes]
```

## Key Hash Mappings for 3.24

### Working Hacks (no changes needed)
These hacks use hashes that exist in both 3.11 and 3.24:
- new_notebook_date_name_hack.qmd
- alphabetize_tags_list_hack.qmd  
- all_mono_hack.qmd
- light_sleep_icon_hack.qmd
- screenshare_everywhere_hack.qmd
- auto_new_page_hack.qmd
- document_pages_hide_hack.qmd

### Toolbar-Related Hashes

| Description | Old Hash (3.11) | New Hash (3.24) | Status |
|-------------|-----------------|-----------------|--------|
| Toolbar component | 2857280009207495592 | **DOES NOT EXIST** | Removed/restructured |
| Toolbar.qml (full path) | ? | 11313888899523275277 | `/qt/qml/xofm/libs/toolbar/qml/Toolbar.qml` |
| Toolbar.qml (basename) | ? | 454089850269675444 | `Toolbar.qml` |

### Important Common Hashes (3.24)
```
FocusScope:                     8397993708429497603
Loader:                         7081645463424
Values:                         7082020628281
root:                           6504254477
visible:                        233748328658231
width:                          214646099849
height:                         214646099627
false:                          6504329801
true:                           6504312620
```

### File Path Hashes (3.24)
```
/qt/qml/xofm/libs/toolbar/qml/Toolbar.qml:           11313888899523275277
/qml/device/view/documentview/DocumentView.qml:      1224665461898798997
/qml/device/view/navigator/Navigator.qml:            8850026134298937527
/qml/device/view/documentview/DeviceSceneView.qml:   11806562588218124596
/qml/device/view/documentview/SceneViewGestures.qml: 17298303916558156758
/qml/common/Values.qml:                              1658694193319203921
```

## Problem: Toolbar Structure Changed

The 11 toolbar-dependent hacks fail because:
1. Hash `2857280009207495592` does not exist in 3.24 hashtab
2. Using the new Toolbar.qml hash `11313888899523275277` works for AFFECT
3. BUT the internal QML structure changed - selectors like `FocusScope > Loader#closeButton` no longer match

### Next Steps
To port toolbar hacks, need to:
1. Extract actual Toolbar.qml source from 3.24 firmware
2. Analyze new component structure
3. Update TRAVERSE selectors to match new hierarchy
4. May need completely different approach for some hacks

## Python Script for Hash Extraction

```python
import struct

with open('hashtab_324', 'rb') as f:
    data = f.read()

pos = 72  # Skip header
entries = {}

while pos < len(data) - 12:
    # BOTH are BIG-ENDIAN!
    hash_val = struct.unpack('>Q', data[pos:pos+8])[0]
    length = struct.unpack('>I', data[pos+8:pos+12])[0]
    
    if length == 0 or length > 500:
        pos += 1
        continue
    
    if pos + 12 + length > len(data):
        break
    
    try:
        string = data[pos+12:pos+12+length].decode('utf-8')
        entries[string] = hash_val
        pos += 12 + length
    except:
        pos += 1

print(f"Extracted {len(entries)} entries")
```
