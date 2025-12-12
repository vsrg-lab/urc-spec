# URC Format Specification v1.0

**Version:** 1.0  
**Date:** 2025-12-12
**Status:** Stable

## Table of Contents

- [Overview](#overview)
- [File Structure](#file-structure)
- [Section Definitions](#section-definitions)
  - [@URC](#urc-header)
  - [@Metadata](#metadata)
  - [@Judgment](#judgment)
  - [@Layout](#layout)
  - [@Timing](#timing)
  - [@Notes](#notes)
- [Data Types](#data-types)
- [Validation Rules](#validation-rules)
- [Examples](#examples)
- [Migration Guide](#migration-guide)

## Overview

URC (Universal Rhythm Chart) is a text-based format for representing vertical scrolling rhythm game (VSRG) charts in a game-agnostic way.

### Design Principles

1. **Text-based** - Human readable, version control friendly
2. **Section-based** - Clear separation of concerns
3. **Game-agnostic** - Works for any VSRG game
4. **ML-friendly** - Easy feature extraction
5. **Lossless** - Preserves original information

### File Properties

- **Extension:** `.urc`
- **Encoding:** UTF-8
- **Line endings:** LF or CRLF (both accepted)
- **Comments:** Lines starting with `#` (outside section headers)

## File Structure
```
@URC <version>

@Metadata
<metadata fields>

@Judgment
<judgment data>

@Layout
<layout data>

@Timing
<timing points>

@Notes
<note entries>
```

### Section Order

Sections must appear in this order:
1. `@URC` (required)
2. `@Metadata` (required)
3. `@Judgment` (required)
4. `@Layout` (required)
5. `@Timing` (required)
6. `@Notes` (required)

## Section Definitions

### @URC (Header)

**Format:**
```
@URC <major>.<minor>
```

**Description:**  
File format version identifier. Must be the first line.

**Example:**
```
@URC 1.0
```

**Fields:**
- `major` - Major version number (breaking changes)
- `minor` - Minor version number (backward compatible)

**Current Version:** 1.0

---

### @Metadata

**Description:**  
Contains song and chart metadata.

#### Required Fields

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `Original` | string | Source game name | `osu!mania` |
| `Title` | string | Song title | `Freedom Dive` |
| `Artist` | string | Song artist | `xi` |
| `Creator` | string | Chart creator | `Nakagawa-Kanon` |
| `Version` | string | Difficulty name | `Four Dimensions` |

**Format:**
```
@Metadata
<Field>: <Value>
```

**Example:**
```
@Metadata
Original: osu!mania
Title: The Glory Days
Artist: Tia
Creator: CloudHolic
Version: Glory
```

**Rules:**
- Field names are case-sensitive
- Values may contain any UTF-8 characters
- Leading/trailing whitespace is trimmed
- Empty values are not allowed

---

### @Judgment

**Description:**  
Defines timing windows and scoring rates for the chart.

**Format:**
```
@Judgment
Window: <w1>, <w2>, <w3>, ...
Rate: <r1>, <r2>, <r3>, ...
```

**Fields:**

#### Window
Timing windows in milliseconds (±ms).

- **Type:** Comma-separated floats
- **Order:** Best to worst judgment
- **Units:** Milliseconds (±)

**Example:**
```
Window: 16.5, 40.5, 73.5, 103.5, 127.5, 164.5
```
Means:
- Marvelous: ±16.5ms
- Perfect: ±40.5ms
- Great: ±73.5ms
- Good: ±103.5ms
- Bad: ±127.5ms
- Miss: +164.5ms

#### Rate
Score percentage for each judgment tier.

- **Type:** Comma-separated floats
- **Range:** 0-100
- **Order:** Must match Window order
- **Units:** Percentage
- **Note:** If the last rate is 0, the correspond window is considered **early Miss**.

**Example:**
```
Rate: 100, 100, 66.67, 33.33, 16.67, 0
```
Means:
- Marvelous: 100% of max score
- Perfect: 100% of max score
- Great: 66.67%
- Good: 33.33%
- Bad: 16.67%
- Miss: 0%

**Complete Example:**
```
@Judgment
Window: 16.5, 40.5, 73.5, 103.5, 127.5, 164.5
Rate: 100, 100, 66.67, 33.33, 16.67, 0
```

**Game Presets:**
```
# osu!mania (OD 8)
Window: 16.5, 40.5, 73.5, 103.5, 127.5, 164.5
Rate: 100, 100, 66.67, 33.33, 16.67, 0

# osu!mania (OD 10)
Window: 16.5, 34.5, 67.5, 97.5, 121.5, 158.5
Rate: 100, 100, 66.67, 33.33, 16.67, 0

# Quaver (Standard)
Window: 18, 43, 76, 106, 127, 164
Rate: 100, 98.25, 65, 25, 0, 0

# DJMAX Respect V
# Note: This preset is NOT official.
Window: 42, 60, 78, 99, 120, 129, 138, 147, 159, 162, 177
Rate: 100, 90, 80, 70, 60, 50, 40, 30, 20, 10, 1

# EZ2ON REBOOT : R (STANDARD)
# Note: This preset is NOT official.
Window: 22, 55, 110
Rate: 100, 70, 20
```

**Rules:**
- Window and Rate must have same number of values
- Window values must be in ascending order
- Rate values must be in descending order
- Rate values must be 0-100

---

### @Layout

**Description:**  
Defines key count and special lane configuration.

**Format:**
```
@Layout
Type: <key_config>
Special: <lane_list> | None
```

**Fields:**

#### Type
Total key configuration.

- **Format:** `<keys>` or `<keys>+<special>`
- **Examples:** `7`, `6+2`, `8+2`, `7+1`

**Standard layouts:**
- `4` - 4K
- `5` - 5K (Pop'n Music style)
- `6` - 6K
- `7` - 7K (standard)
- `8` - 8K
- `10` - 10K (DP)

**Special layouts:**
- `6+2` - 6 normal + 2 side keys
- `7+1` - 7 normal + 1 scratch

#### Special
0-indexed lane numbers for special keys.

- **Format:** Comma-separated integers or `None`
- **Range:** 0 to (total_keys - 1)

**Examples:**
```
# Standard 7K
@Layout
Type: 7
Special: None

# DJMAX 4+2
@Layout
Type: 4+2
Special: 0, 5

# DJMAX 8+2
@Layout
Type: 8+2
Special: 0, 9

# BMS 7+1 (scratch on left)
@Layout
Type: 7+1
Special: 0
```

**Visual Layouts:**
```
7K:
┌───────────────┐
│ 0 1 2 3 4 5 6 │
└───────────────┘

4+2 (DJMAX):
┌─────────────────┐
│ [0] 1 2 3 4 [5] │
└─────────────────┘
  SD          SD

7+1 (BMS):
┌───────────────────┐
│ [0] 1 2 3 4 5 6 7 │
└───────────────────┘
  SC
```

**Rules:**
- Type must match total lane count in notes
- Special lanes must be valid lane indices
- Special lanes cannot be duplicated

---

### @Timing

**Description:**  
BPM and time signature information.

**Format:**
```
@Timing
<timestamp>, <bpm>, <meter>
```

**Fields:**
- `timestamp` - Time in milliseconds (integer)
- `bpm` - Beats per minute (float)
- `meter` - Time signature (string)

**Example:**
```
@Timing
0, 184, 4/4
```

**Multiple BPM changes:**
```
@Timing
0, 180, 4/4
45500, 222.22, 4/4
120000, 184, 3/4
```

**Common Meters:**
- `4/4` - Standard (most common)
- `3/4` - Waltz time
- `7/8` - Odd meter
- `6/8` - Compound meter

**Rules:**
- First timing point must start at 0
- Timestamps must be in ascending order
- BPM must be positive
- Meter format: `<beats>/<note_value>`

---

### @Notes

**Description:**  
Note data - the core of the chart.

**Format:**
```
@Notes
<timestamp>, <lane>, <type>
```

**Fields:**
- `timestamp` - Time in milliseconds (integer)
- `lane` - Lane number, 0-indexed (integer)
- `type` - Note type (string)

**Note Types:**

| Type | Description | Usage |
|------|-------------|-------|
| `N` | Normal note | Standard tap |
| `LS` | Long note start | Hold beginning |
| `LE` | Long note end | Hold ending |
| `M` | Mine | Avoid note (damage) |
| `F` | Fake | Visual only (no judgment) |

**Example:**
```
@Notes
500, 0, N
1000, 2, LS
1100, 6, N
1500, 2, LE
2000, 3, N
2500, 1, LS
3000, 4, N
3500, 1, LE
```

**Long Note Pairing:**

Long notes (`LS` and `LE`) are automatically paired:
- Same lane
- Time order (earlier LS pairs with next LE)
```
1000, 2, LS  ─┐
1500, 2, LE  ─┘  Pair 1

2000, 2, LS  ─┐
2500, 2, LE  ─┘  Pair 2
```

**Invalid (unpaired):**
```
1000, 2, LS
1500, 2, LS  ❌ No LE for first LS
2000, 2, LE  ❌ Which LS does this match?
```

**Rules:**
- Timestamps should be in ascending order (recommended)
- Lane must be valid (0 to key_count-1)
- LS must have matching LE on same lane
- LE must come after corresponding LS
- No overlapping long notes on same lane

---

## Data Types

### Integer
Whole numbers, no decimals.
```
0
1000
-5
```

### Float
Decimal numbers.
```
16.5
222.22
-3.14
```

### String
UTF-8 text, no quotes needed.
```
Title: Freedom Dive
Artist: xi
```

### List
Comma-separated values.
```
Window: 16.5, 40.5, 73.5
Special: 0, 7
```

---

## Validation Rules

A valid URC file must satisfy:

### Structural Rules
1. ✅ First line is `@URC <version>`
2. ✅ All required sections present
3. ✅ Sections in correct order
4. ✅ All required fields present

### Metadata Rules
5. ✅ All required metadata fields have values
6. ✅ Field names are valid

### Judgment Rules
7. ✅ Window and Rate have same count
8. ✅ Window values ascending
9. ✅ Rate values descending
10. ✅ Rate values in range 0-100

### Layout Rules
11. ✅ Type matches note lane count
12. ✅ Special lanes are valid indices
13. ✅ No duplicate special lanes

### Timing Rules
14. ✅ First timing point at timestamp 0
15. ✅ Timestamps ascending
16. ✅ BPM positive
17. ✅ Valid meter format

### Notes Rules
18. ✅ All lanes valid (0 to key_count-1)
19. ✅ Valid note types
20. ✅ LS/LE properly paired
21. ✅ No overlapping long notes on same lane
22. ✅ Timestamps non-negative

### Recommended (not required)
- 📝 Notes sorted by timestamp
- 📝 Comments for clarity
- 📝 Consistent indentation

---

## Examples

See [examples/](examples/) directory:

- `basic.urc` - Simple chart
- `complex.urc` - BPM changes, long notes
- `djmax_6+2.urc` - DJMAX layout
- `bms_7+1.urc` - BMS scratch layout
- `stepmania.urc` - StepMania conversion

---

## Version History

### v1.0 (2025-12-12)
- Initial specification
- Core sections defined
- Basic note types
- Long note support
- Special lane support

---

## References

- [osu! File Format](https://osu.ppy.sh/wiki/en/Client/File_formats/osu_%28file_format%29)
- [BMS Format Specification](https://hitkey.nekokan.dyndns.info/cmds.htm)
- [StepMania .sm Format](https://github.com/stepmania/stepmania/wiki/sm)
- [Quaver .qua Format](https://github.com/Quaver/Quaver.API/wiki/.qua-file-format)

---

**Specification maintained by:** [VSRG Lab](https://github.com/vsrg-lab)  
**Last updated:** 2025-12-12
