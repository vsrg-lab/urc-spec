# URC (Universal Rhythm Chart) Format
A universal, text-based format for representing vsrg charts across multiple games.

<p align="center">
  <img src="https://img.shields.io/badge/version-1.0-blue.svg" alt="Version">
  <img src="https://img.shields.io/badge/license-MIT-green.svg" alt="License">
</p>

## Overview

URC is designed to be:

- **Cross-game compatible** - Supports osu!mania, BMS, StepMania, Quaver, and more
- **ML/analysis friendly** - Clean structure for feature extraction and machine learning
- **Human readable** - Simple text format that's easy to read and write
- **Parser friendly** - Straightforward parsing logic for any programming language
- **Metadata rich** - Preserves judgment systems, layouts, and timing information

## Quick Example
```urc
@URC 1.0

@Metadata
Original: osu!mania
Title: Freedom Dive
Artist: xi
Creator: razlteh
Version: Blocko's 7K Black Another

@Judgment
Window: 16.5, 40.5, 73.5, 103.5, 127.5
Rate: 100, 100, 66.67, 33.33, 16.67

@Layout
Type: 7
Special: None

@Timing
0, 222.22, 4/4

@Notes
500, 0, N
1000, 2, LS
1500, 2, LE
2000, 4, N
```

## 📚 Documentation

- **[Format Specification](SPEC.md)** - Complete technical specification
- **[Examples](examples/)** - Sample URC files for various scenarios
- **[Changelog](CHANGELOG.md)** - Version history and updates

## 🎮 Supported Source Formats

| Format | Game | Extension | Status |
|--------|------|-----------|--------|
| osu!mania | osu! | `.osu` | ✅ Supported |
| BMS | beatmania IIDX | `.bms`, `.bme`, `.bml` | ✅ Supported |
| StepMania | StepMania | `.sm`, `.ssc` | ✅ Supported |
| Quaver | Quaver | `.qua` | ✅ Supported |
| O2Jam | O2Jam | `.ojn` | 🔜 Planned |
| DJMAX | DJMAX | N/A | 🔜 Planned |

## 🚀 Quick Start

### Converting a Chart
```bash
# Using urc-converter
urc-converter convert chart.osu -o chart.urc
```

### Parsing in Python
```python
from urc_parser import UrcParser

parser = UrcParser()
chart = parser.parse('chart.urc')

print(f"Title: {chart.metadata.title}")
print(f"Keys: {chart.layout.type}")
print(f"Notes: {len(chart.notes)}")
```

### Parsing in C#
```csharp
using VsrgLab.UrcParser;

var parser = new UrcParser();
var chart = parser.Parse("chart.urc");

Console.WriteLine($"Title: {chart.Metadata.Title}");
Console.WriteLine($"Keys: {chart.Layout.Type}");
Console.WriteLine($"Notes: {chart.Notes.Count}");
```

## 📖 Format Overview

URC files consist of sections marked with `@` prefix:
```
@URC 1.0              // Version header

@Metadata             // Song and chart information
Original: <game>
Title: <title>
...

@Judgment             // Timing windows and scoring
Window: <windows>
Rate: <rates>

@Layout               // Key configuration
Type: <keys>
Special: <special_lanes>

@Timing               // BPM changes
<time>, <bpm>, <meter>

@Notes                // Note data
<time>, <lane>, <type>
```

See [SPEC.md](SPEC.md) for complete details.

## Design Goals

1. **Simplicity** - Easy to implement parsers in any language
2. **Completeness** - Preserve all information needed for analysis
3. **Neutrality** - No game-specific bias
4. **Extensibility** - Easy to add new features without breaking compatibility
5. **Readability** - Humans can understand files without tools

## Contributing

Contributions are welcome! Please feel free to:
- Report issues
- Suggest improvements
- Submit pull requests
- Add examples

## 📝 License

MIT

---

**Note:** This is a research project developed as part of graduate studies (2026-2028) on ML-based difficulty prediction for rhythm games.
