# Nova

An accessible ebook reader, written primarily in Rust.

## Installing

From the [latest release](https://github.com/Khalil220/Nova/releases/latest), pick one:

- **Installer** (the `nova-setup` `.exe`): per-user (no admin), and offers to make Nova the default reader for the types you tick (ebooks pre-ticked). Data lives in `%APPDATA%\Nova`.
- **Portable** (the `.zip`): keeps its data in a `data` folder beside `nova.exe`, and registers nothing. Nova uses this layout whenever a `data` folder or a `portable` marker sits next to the exe.
- **Android** (the `.apk`): Android 8.0 or newer, installed directly rather than from the Play Store. Its data lives with the app, so uninstalling clears it.

To switch a portable copy to installed, run the installer and move your old `data` contents into `%APPDATA%\Nova`.

## Formats

- **EPUB** (2 and 3)
- **PDF**
- **DOCX**
- **PPTX**: PowerPoint decks, one slide per chapter
- **MOBI / AZW / AZW3 / PRC**
- **Markdown**
- **HTML / XHTML**
- **Plain text**: UTF-8, UTF-16 and Windows-1252
- **DAISY**: 2.02 (`ncc.html`) and DTBook XML

Tables are read out with their column headers ("Quarter: Q1; Revenue: 120"), images announce their alt text, nested lists use a different bullet per level, and DOCX footnotes and list numbering come through.

## Reading

- `Ctrl+T`: table of contents, opens on the chapter you're in
- `Ctrl+G`: go to line or page
- `Ctrl+F` / `F3` / `Shift+F3`: find, find next, find previous
- `Ctrl+B` / `Ctrl+Shift+B`: add / manage bookmarks
- `Ctrl+H` / `Ctrl+Shift+H`: highlight selection / manage highlights
- `Alt+Left` / `Alt+Right`: navigation history

Find searches the whole book, with Match case and Whole word options. It ignores differences in quotes, dashes, and accents. A straight apostrophe finds a curly one, `cafe` finds `café`. It tells you "match 3 of 47" as you go, and highlights every match.

Your position is autosaved and restored on reopen.

## Library

Books are only added when you add them: `Ctrl+D` for the current book, or "Add books…" in the library dialog (`Ctrl+L`).

Each book tracks a reading status (want to read, not started, reading, finished), favorite flag, and how many times you've read it. You can search, filter by status, sort by title/author/progress/recency, or arrange a custom order with Move Up/Down. Enter opens the focused book, Delete removes it.

Sorting ignores leading articles and sorts authors by surname ("The Hobbit" under H, "J.R.R. Tolkien" under T). A per-book "Sort as…" override covers the cases the heuristic gets wrong.

## Preferences (`Ctrl+P`)

- **Font family / size**: the font and size used in the reading area.
- **Autosave interval**: how often your position (and, for library books, reading progress) is saved while reading. Closing the book always saves immediately.
- **Maximum recent files**: length of the File > Recent list.
- **Highlight color**: the background color drawn behind highlighted text.
- **Log level / retention**: how verbose the log file is and how many days are kept.
- **PDF table detection**: reconstruct aligned text grids as tables. Works well on real tables; can misfire on unusual layouts like two-column verse, hence the switch.
- **Automatically mark books finished**: reaching the end of a book sets it to Finished in the library.
- **Recognize re-reads**: reopening a finished book and starting over counts a new read.
- **Library list**: show the author before the title, and/or compact rows (title and author only, less per row).
