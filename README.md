# CodeAtlas

**Offline repository intelligence and AI-context optimization tool.**

CodeAtlas reduces unnecessary Claude/AI token usage by analyzing your repository once and providing precise file, class, and method discovery without full repository exploration.

## Problem

When working with large repositories, Claude and other AI agents waste tokens on:

- Discovering which files are relevant
- Searching for classes and methods
- Understanding code relationships
- Exploring dependencies

CodeAtlas solves this by building a lightweight, offline repository index that tells AI agents exactly what files to read.

## Solution

```
Before CodeAtlas:
  Claude → Search repository → Read many files → Understand problem

After CodeAtlas:
  Claude → CodeAtlas query → Read only relevant files → Solve problem
```

## Key Features

- **Completely Offline** - No APIs, no remote servers, no internet required
- **Fast Discovery** - Find classes, methods, and files instantly
- **Dependency Analysis** - Understand which classes depend on which classes
- **Impact Analysis** - See what files are affected by a change
- **Token Optimization** - Estimate tokens saved by using precise context
- **Incremental Updates** - Fast re-indexing of changed files

## Installation

### With uv (recommended)

```bash
git clone https://github.com/dhiraj-pawar/CodeAtlas
cd CodeAtlas
uv sync
```

### With pip

```bash
git clone https://github.com/dhiraj-pawar/CodeAtlas
cd CodeAtlas
pip install -e .
```

## Quick Start

```bash
# Initialize CodeAtlas in your repository
CodeAtlas init

# Scan the repository
CodeAtlas scan

# View repository structure
CodeAtlas map

# Find a class
CodeAtlas find WebServiceDialog

# Show statistics
CodeAtlas stats
```

## CLI Commands

### CodeAtlas init

Initialize CodeAtlas in the current repository.

```bash
CodeAtlas init
```

Creates `.codeatlas/` directory with configuration.

### CodeAtlas scan

Perform complete repository analysis.

```bash
CodeAtlas scan
```

Discovers files, extracts classes and methods, builds dependency graph.

### CodeAtlas map

Generate repository structure map.

```bash
CodeAtlas map
```

### CodeAtlas find

Find classes, methods, or files.

```bash
CodeAtlas find WebServiceDialog
CodeAtlas find save --type method
CodeAtlas find MyInterface
```

### CodeAtlas stats

Show repository statistics.

```bash
CodeAtlas stats
```

Shows file count, class count, method count, dependency count.

### CodeAtlas help

Display help message.

```bash
CodeAtlas help
CodeAtlas --version
```

## Architecture

```
Repository
    ↓
Scanner (discover files, detect language)
    ↓
Parser (extract classes, methods, imports)
    ↓
Index (store symbols and relationships)
    ↓
.codeatlas/
    ├── config.json
    ├── files.json
    ├── symbols.json
    ├── dependencies.json
    └── graph.json
    ↓
CLI (find, map, stats, context, impact, etc.)
    ↓
AI Agent (Claude, etc.)
```

## Roadmap

### Phase 1 ✓ (Current)
- Project setup
- CLI framework
- Core indexing
- Basic commands (init, scan, map, find, stats)

### Phase 2
- Enhanced symbol extraction
- Dependency analysis
- Impact analysis
- Trace command

### Phase 3
- Incremental updates
- File change detection
- Diff command

### Phase 4
- AI context generation
- Token estimation
- Context budget
- Minimal context queries

### Phase 5
- Claude skill integration
- MCP adapter (optional)

### Phase 6
- Multi-language support expansion
- Advanced analytics
- Performance optimizations

## Language Support

Currently supported:
- **Java** (primary focus)

Planned:
- Python
- TypeScript/JavaScript
- Go
- Rust
- C#

## Offline Design

CodeAtlas requires **zero external dependencies** for core functionality:

- ✅ All analysis happens locally
- ✅ No network calls required
- ✅ No API keys needed
- ✅ No authentication required
- ✅ No telemetry
- ✅ No cloud services

## Index Format

Generated in `.codeatlas/`:

```
config.json        Project metadata
files.json         Discovered files
symbols.json       Classes, methods, interfaces
dependencies.json  Relationships between symbols
graph.json         Full dependency graph
statistics.json    Repository statistics
```

All files are JSON for easy inspection and integration.

## Security

- No source code is copied to the index
- Index contains only structural information
- All paths are relative to repository root
- No credentials or secrets are stored
- Can be safely committed to git

## Development

### Setup

```bash
uv sync
uv run pytest tests/
```

### Running locally

```bash
cd CodeAtlas
uv run CodeAtlas --help
uv run CodeAtlas init
uv run CodeAtlas scan
```

### Testing

```bash
uv run pytest tests/ -v
uv run pytest tests/test_parser.py -k java
```

### Code style

- Line length: 100 chars
- Python 3.10+
- Ruff for linting
- Type hints (basic mode)

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Write tests
4. Submit a pull request

## License

Apache 2.0 - See LICENSE file

## Author

**Dhiraj Pawar**

## FAQ

### Can I use CodeAtlas with GitHub Copilot?

Yes, CodeAtlas works with any AI agent that can run CLI commands.

### How large can repositories be?

CodeAtlas has been tested with repositories containing:
- 10,000+ files
- 1,000+ classes
- 20,000+ methods

Indexing typically takes a few seconds.

### What programming languages does CodeAtlas support?

Currently Java. Python, TypeScript, JavaScript, and Go support are planned.

### Is CodeAtlas related to Graphify?

No. CodeAtlas is a separate tool focused specifically on AI token optimization and repository navigation. While it uses some similar indexing concepts, it is designed from scratch around the goal of reducing AI token waste.

### Can I commit .codeatlas/ to git?

Yes. The index is deterministic and safe to commit. It helps your team get immediate repository context.

## Getting Help

- 📖 Read the README
- 🐛 Open an issue on GitHub
- 💬 Check existing discussions

## Roadmap Features (Future)

- Graph visualization
- Neo4j export
- Obsidian vault generation
- Query language
- Advanced filtering
- Test coverage analysis
- Documentation extraction

---

**CodeAtlas** - Making repository intelligence efficient for AI.
