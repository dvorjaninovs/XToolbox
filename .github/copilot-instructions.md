# XToolBox Copilot Instructions

## Project Overview
XToolBox is an **archived** Windows 10/11 debloating utility written in Python. The project consists of:
- A CLI tool (`main.py`) that provides an interactive menu for downloading and running various Windows optimization tools
- A simple static website (`site/`) for distribution via PowerShell one-liner
- A collection of pre-made batch files (`files/`) for common tweaks

**⚠️ Important: This project is archived and no longer actively maintained.**

## Architecture & Data Flow

### Core Components
1. **`main.py`** - Main CLI interface with Rich-based table UI and command interpreter
2. **`xtools.py`** - Tool definitions, download URLs, and web scraping utilities for various software
3. **`tools.py`** - UI utilities (quotes, footers, column layouts)
4. **`site/`** - Vite-based static site with Tailwind CSS (Catppuccin theme)

### Key Data Structures
- **`Tool` class**: Represents downloadable utilities with metadata, URLs, and download options
- **`Dwn` class**: Handles dynamic URL assembly for software with changing download links
- **`tools` dictionary**: Central registry of all available tools, keyed by page-position IDs (e.g., "d1-1", "d2-3")

### Navigation Pattern
The UI uses a page-based system:
- Pages 1-4: Tool selection grids (4 columns × 15 rows per page)
- Page 0: Help system
- Special pages (97, 98): EULA and similar dialogs
- Commands: `n`/`b` (next/back), `h` (help), `i <code>` (info), `99` (exit)

## Development Workflows

### Testing the CLI
```powershell
# Install dependencies
pip install -r requirements.txt

# Run the main application
python main.py
```

### Building the Website
```bash
cd site
npm install
npm run dev    # Development server
npm run build  # Production build
```

### Tool Management
- Tools are defined in `xtools.py` starting at line 266 in the `tools` dictionary
- Each tool has a unique ID format: `d{column}-{page}` (e.g., "d1-1" = column 1, page 1)
- Use `iScrape` class methods to dynamically fetch latest versions of Linux distributions
- Use `latest()` function for GitHub release version checking

## Project-Specific Conventions

### Logging & Error Handling
- Uses Python's `logging` module with file output to `xtoolbox.log`
- Rich console for user-facing output with color-coded status messages:
  - `Printer.green()` for success (`[✓]`)
  - `Printer.yellow()` for warnings (`[?]`)
  - `Printer.red()` for errors (`[✗]`)
  - `Printer.zpr()` for info (`[>]`)

### Download Management
- Custom `download()` function with Rich progress bars
- Uses `requests.Session` with HTTP adapter for optimized downloads
- Files are downloaded to current directory, executed, then optionally cleaned up

### UI Patterns
- `pageDisplay(n)` renders the main table interface for page n
- `interpreter(page)` handles user input and command routing
- Random quotes system via `chooseQuotes()` for flavor text
- Consistent 4-column layout defined in `tools.py:columns`

## External Dependencies
- **Rich**: Console UI, tables, progress bars, styling
- **Requests**: HTTP downloads and API calls
- **BeautifulSoup4**: Web scraping for software versions
- **PSUtil**: System information (used minimally)

## Distribution Model
The app is distributed via a PowerShell one-liner that downloads the compiled executable:
```powershell
irm https://xtoolbox.fuckingbitch.eu/get | iex
```

This fetches the latest GitHub release and auto-runs it. The `/get` endpoint serves a PowerShell script, not the main Python code.

## Integration Points
- GitHub API for release version checking
- Various software vendor websites for download link scraping
- Static file hosting for the website and PowerShell script
- PyInstaller compilation pipeline (not in repo)

When modifying this codebase, maintain the existing page-based navigation system and tool ID conventions. All new tools should follow the `Tool`/`Dwn` class pattern for consistency.