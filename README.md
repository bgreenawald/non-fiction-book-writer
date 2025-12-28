# Business Book Writer

A Python CLI tool that generates complete book drafts from detailed markdown outlines using LLMs via OpenRouter.

## Features

- **Multi-book support**: Each book lives in its own directory with rubric and config
- **Parallel chapter generation**: Chapters are processed concurrently (configurable limit)
- **Sequential section building**: Each section builds on previous sections within a chapter
- **Resume capability**: Failed sections can be retried without re-generating completed work
- **State persistence**: Progress is saved after each section
- **PDF/EPUB export**: Convert generated markdown to PDF and EPUB using Pandoc

## Installation

```bash
# Clone the repository
git clone <repository-url>
cd business-book-writer

# Install with UV
uv sync

# Or install with pip
pip install -e .
```

## Configuration

### Environment Variables

Create a `.env` file or set environment variables:

```bash
BOOKWRITER_OPENROUTER_API_KEY=sk-or-...
BOOKWRITER_DEFAULT_MODEL=anthropic/claude-sonnet-4
BOOKWRITER_MAX_CONCURRENT_CHAPTERS=5
```

### Per-Book Configuration

Each book can have a `config.yaml` file:

```yaml
title: "My Book Title"
model: anthropic/claude-3-opus  # Override default model
temperature: 0.7
max_tokens_per_section: 4000
max_concurrent_chapters: 5
```

## Usage

### Initialize a New Book

```bash
uv run bookwriter init ./books/my-new-book --title "My Book Title"
```

This creates:
- `config.yaml`: Book settings
- `rubric.md`: Template outline to fill in
- `output/`: Directory for generated content

### Edit the Rubric

The rubric defines your book structure:
- `#` (H1) headers define chapters
- `##` (H2) headers define sections
- `###` (H3+) content provides instructions for each section

Example:
```markdown
# Chapter 1: Introduction

## 1.1 Opening Hook

### What to cover
- Start with a compelling anecdote
- Introduce the main theme
- Hook the reader

## 1.2 Background Context

### What to cover
- Historical background
- Why this matters now
```

### Generate the Book

```bash
# Generate entire book
uv run bookwriter generate ./books/my-book

# Generate specific chapters
uv run bookwriter generate ./books/my-book --chapters 1,2,3

# Use a different model
uv run bookwriter generate ./books/my-book --model anthropic/claude-3-opus
```

### Check Status

```bash
uv run bookwriter status ./books/my-book
```

### Resume After Failures

```bash
uv run bookwriter resume ./books/my-book
```

### Combine and Convert

```bash
# Combine chapters into book.md
uv run bookwriter combine ./books/my-book

# Convert to PDF and EPUB
uv run bookwriter convert ./books/my-book

# Convert to PDF only
uv run bookwriter convert ./books/my-book --format pdf
```

### List All Books

```bash
uv run bookwriter list ./books
```

## Project Structure

```
business-book-writer/
├── pyproject.toml
├── src/
│   └── book_writer/
│       ├── cli.py          # CLI commands
│       ├── parser.py       # Rubric parsing
│       ├── generator.py    # Generation orchestration
│       ├── openrouter.py   # LLM API client
│       ├── state.py        # Progress persistence
│       └── converter.py    # PDF/EPUB conversion
├── books/
│   └── business-literacy/  # Example book
│       ├── rubric.md
│       ├── config.yaml
│       └── output/
│           ├── state.json
│           ├── chapters/
│           ├── book.md
│           ├── book.pdf
│           └── book.epub
└── tests/
```

## Requirements

- Python 3.11+
- OpenRouter API key
- Pandoc (for PDF/EPUB conversion)
- LaTeX (for PDF generation, e.g., texlive-xetex)

## License

MIT
