# Nova

An ebook reader for Windows, written in Rust.

The reading area is a standard Windows text control, so NVDA, JAWS and Narrator read it like any normal document, with their usual commands. The rest of the UI is plain menus and dialogs for the same reason.

## Formats

- **EPUB** (2 and 3)
- **PDF**
- **DOCX**
- **MOBI / AZW / AZW3 / PRC**
- **Markdown**
- **HTML / XHTML**
- **Plain text** — UTF-8, UTF-16 and Windows-1252
- **DAISY** — 2.02 (`ncc.html`) and DTBook XML

Tables are read out with their column headers ("Quarter: Q1; Revenue: 120"), images announce their alt text, nested lists use a different bullet per level, and DOCX footnotes and list numbering come through.

## Reading

The book is one scrollable document with normal caret navigation, so screen reader reading commands behave as they would in any editor.

- `Ctrl+T` — table of contents, opens on the chapter you're in, not the top
- `Ctrl+G` — go to line or page
- `Ctrl+F` / `F3` — find / find next
- `Ctrl+B` / `Ctrl+Shift+B` — add / manage bookmarks
- `Ctrl+H` / `Ctrl+Shift+H` — highlight selection / manage highlights
- `Alt+Left` / `Alt+Right` — navigation history
- `Ctrl+Home` / `Ctrl+End` — start / end of the whole book

Your position is autosaved and restored on reopen.

## Library

Books are only added when you add them: `Ctrl+D` for the current book, or "Add books…" in the library dialog (`Ctrl+L`).

Each book tracks a reading status (want to read, not started, reading, finished), favorite flag, and how many times you've read it. You can search, filter by status, sort by title/author/progress/recency, or arrange a custom order with Move Up/Down. Enter opens the focused book, Delete removes it.

Sorting ignores leading articles and sorts authors by surname ("The Hobbit" under H, "J.R.R. Tolkien" under T). A per-book "Sort as…" override covers the cases the heuristic gets wrong.

## Preferences (`Ctrl+P`)

- **Font family / size** — reader font. The family box lists your installed fonts.
- **Chapters to load ahead** — how far past your position Nova pre-loads (see below).
- **Maximum loaded chapters** — cap on how many chapters stay in memory at once.
- **Autosave interval** — how often your position (and, for library books, reading progress) is saved while reading. Closing the book always saves immediately.
- **Maximum recent files** — length of the File > Recent list.
- **Highlight color** — background color for highlights, chosen from a list of named colors.
- **Log level / retention** — how chatty the log file is and how many days are kept.
- **PDF table detection** — reconstruct aligned text grids as tables. Works well on real tables; can misfire on unusual layouts like two-column verse, hence the switch.
- **Automatically mark books finished** — reaching the end of a book sets it to Finished in the library.
- **Recognize re-reads** — reopening a finished book and starting over counts a new read.
- **Library list** — show the author before the title, and/or compact rows (title and author only — less to listen to per row).

## How it loads books

**EPUB.** The zip is opened once and read randomly: when a chapter is needed, only that entry is decompressed, straight out of the archive. Chapters load in spine order in a sliding window — the ones you're reading plus the lookahead — and old ones are dropped once the window hits the cap, so a 2,000-chapter book costs about the same memory as a 20-chapter one. Jumping via the TOC or Go To loads from the target, not everything before it.

**Plain text.** Read once, decoded tolerantly (UTF-8 with or without BOM, UTF-16 either endianness, Windows-1252 fallback), then split into fixed-size sections that act as virtual chapters so navigation stays fast on 20 MB files.

**PDF.** PDFium is loaded lazily the first time you open a PDF, so it costs nothing otherwise. Each page is a chapter. Text is rebuilt into reading order from the raw glyph positions; aligned grids can be detected as tables. If the PDF is tagged, Nova reads the structure tree directly — figure alt text and real table semantics — instead of guessing.

**DOCX / MOBI / HTML / Markdown / DAISY.** Parsed once into titled chapters, split at headings, then read through the same chapter machinery as everything else. MOBI handles PalmDoc decompression itself; HUFF/CDIC books go through the mobi crate.

**When files are bad.** Malformed markup falls back to a regex extractor instead of failing, absurdly deep XML nesting is caught before it can overflow the stack, and corrupt files get an error dialog, not a crash.

