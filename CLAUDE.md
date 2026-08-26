# CLAUDE.md

This file provides guidance to Claude Code when working with the CodeAtlas codebase.

## Project Overview

**CodeAtlas** is an offline repository intelligence and AI-context optimization tool designed to reduce unnecessary Claude/AI token consumption during repository exploration.

**Primary goal**: Enable AI agents to find relevant code without wasting tokens on full repository discovery.

**Key design principle**: Structure + Precision, not Graphs + Visualization

## Architecture

```
Repository → Scanner → Parser → Index → CLI Commands → AI Agent
```

### Core Modules

| Module | Purpose |
|--------|---------|
| `core.py` | CodeAtlas engine, indexing, graph storage |
| `cli.py` | Command handlers for all CodeAtlas commands |
| `scanner.py` | File discovery and language detection |
| `parsers/` | Language-specific extraction (Java, Python, etc.) |
| `exporters/` | Export index in various formats |

### Data Model

Generated in `.codeatlas/`:

```json
{
  "config.json": "Project metadata",
  "files.json": "File inventory with hashes",
  "symbols.json": "Classes, methods, interfaces",
  "dependencies.json": "Relationships",
  "graph.json": "Full NetworkX graph"
}
```

## Development Workflow

### Setup

```bash
cd D:\CodeAtlas
uv sync
uv run CodeAtlas init
uv run CodeAtlas scan
```

### Testing

```bash
uv run pytest tests/ -q
uv run pytest tests/test_parser.py -k java
```

### CLI Usage

```bash
uv run CodeAtlas --help
uv run CodeAtlas init
uv run CodeAtlas scan
uv run CodeAtlas find WebServiceDialog
uv run CodeAtlas stats
```

## Implementation Phases

### Phase 1 (Current) ✓
- CLI framework
- Core indexing
- File discovery
- Commands: `init`, `scan`, `map`, `find`, `stats`

### Phase 2
- Enhanced parsing
- Dependency analysis
- `class`, `method`, `impact`, `trace` commands

### Phase 3
- Incremental updates
- `update`, `diff` commands

### Phase 4
- AI context engine
- `context` command with token budgets
- Token estimation

### Phase 5+
- Claude skill integration
- Multi-language support
- Advanced analytics

## Key Design Decisions

1. **Offline First** - No network calls, no APIs required
2. **Minimal Storage** - Index contains structure, not source code
3. **Deterministic** - Same input always produces same output
4. **Incremental** - Fast re-indexing when files change
5. **AI-Optimized** - Output designed for machine consumption

## Important Conventions

### Naming
- All CLI commands: `CodeAtlas <command>`
- Never use `graphify` in public CLI
- Index directory: `.codeatlas/`

### Code Style
- Line length: 100 chars
- Python 3.10+
- No comments unless non-obvious WHY
- Type hints (basic mode)
- Imports: alphabetized

### File Organization
- One module per responsibility
- Parsers in `parsers/` directory
- Each language gets its own parser
- Tests mirror source structure

## Testing

### Test Structure

```
tests/
  ├── test_core.py
  ├── test_cli.py
  ├── test_scanner.py
  ├── test_parser.py
  ├── fixtures/
  │   ├── sample.java
  │   ├── sample.py
  │   └── ...
  └── conftest.py
```

### Test Rules

- **No side effects** - All tests use temporary directories
- **No network calls** - Everything is local
- **Deterministic** - Same input always produces same output
- **Fast** - Unit tests run in milliseconds
- **Isolated** - Tests don't depend on execution order

## Important Files

| File | Purpose |
|------|---------|
| `pyproject.toml` | Dependencies, package metadata |
| `README.md` | User documentation |
| `CLAUDE.md` | This file - developer guidance |
| `codeatlas/__main__.py` | CLI entry point |
| `codeatlas/core.py` | Core indexing engine |
| `codeatlas/cli.py` | Command handlers |
| `codeatlas/scanner.py` | Repository scanner |
| `codeatlas/parsers/java_parser.py` | Java language parser |

## Adding a New Language

1. Create `codeatlas/parsers/<lang>_parser.py`
2. Implement `parse(path, root) -> (classes, methods, dependencies)`
3. Register in `scanner.py` LANGUAGE_EXTENSIONS
4. Add tree-sitter dependency to `pyproject.toml`
5. Add fixtures to `tests/fixtures/sample.<ext>`
6. Add tests to `tests/test_parser.py`

## Debugging

### Common Issues

**"CodeAtlas is not initialized"**
```bash
CodeAtlas init
```

**"No index found"**
```bash
CodeAtlas scan
```

**Parser fails on file**
- Check encoding (UTF-8)
- Review parser output
- Update parser if needed

### Debug Output

```bash
# Enable verbose output
uv run CodeAtlas scan --verbose

# Inspect generated files
cat .codeatlas/config.json
cat .codeatlas/symbols.json
```

## Performance Expectations

| Repository Size | Scan Time | Index Size |
|---|---|---|
| 100 files | <1s | <1MB |
| 1,000 files | 1-2s | 2-5MB |
| 10,000 files | 5-10s | 20-50MB |

## Git Workflow

- Branch: `main` (active development)
- Commits: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`
- Before push: `uv run pytest tests/ -q`
- No external dependencies without discussion

## Important Success Criteria

✅ Commands work correctly
✅ Generated index is accurate
✅ Incremental updates work
✅ No network calls
✅ All commands start with `CodeAtlas`
✅ Completely offline
✅ Fast (sub-second queries)
✅ Deterministic output
✅ No external APIs required

## Future Extensions (Don't Implement Now)

- Graph visualization (future, not Phase 1)
- MCP server (future, optional)
- Cloud sync (never - offline only)
- AI models (future, optional)
- Multi-language support expansion
- Query language
- Neo4j export
- Obsidian vault

## Quick References

### Environment Variables

```bash
# Optional - already defaults to .codeatlas
CODEATLAS_INDEX_DIR=/custom/path

# Optional - control languages
CODEATLAS_LANGUAGES=java,python
```

### JSON Output Example

```bash
uv run CodeAtlas find WebServiceDialog --json
```

Returns:
```json
{
  "name": "WebServiceDialog",
  "type": "class",
  "file": "src/.../WebServiceDialog.java",
  "line": 42
}
```

## When in Doubt

1. **Keep it simple** - Implement minimum viable feature
2. **Make it fast** - Index should be quick
3. **Make it offline** - Never add network calls
4. **Make it deterministic** - Same input = same output
5. **Make it testable** - All functionality has tests

---

**CodeAtlas** - Offline repository intelligence for AI token optimization.
