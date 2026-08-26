# CodeAtlas Architecture

## Overview

CodeAtlas is an offline repository intelligence and AI-context optimization tool. It analyzes a repository once and provides precise file, class, and method discovery without full repository exploration.

## Design Principles

1. **Offline First** - No network calls, no APIs, no remote services
2. **Minimal Storage** - Index contains structure only, not source code
3. **Deterministic** - Same input always produces same output
4. **Incremental** - Fast re-indexing when files change
5. **AI-Optimized** - Output designed for machine consumption

## Architecture Diagram

```
┌─────────────────────┐
│   Repository        │
│   (Source Code)     │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   Scanner           │  Discover files
│                     │  Detect languages
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   Parser            │  Extract symbols
│   (Language-        │  Extract relationships
│    Specific)        │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   Index             │  Store metadata
│   (In-Memory)       │  Build graph
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   Disk Storage      │  .codeatlas/
│   (.codeatlas/)     │  config.json
│                     │  files.json
│                     │  symbols.json
│                     │  dependencies.json
│                     │  graph.json
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   CLI Commands      │  find, map, stats
│                     │  context (Phase 4)
│                     │  impact (Phase 2)
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   AI Agent          │  Claude
│   (Consumer)        │  Other tools
└─────────────────────┘
```

## Module Responsibilities

### Core Modules

#### `core.py`
- **CodeAtlas** class - main engine
- **IndexMetadata** - project metadata
- Index initialization
- Symbol and dependency management
- Graph storage (NetworkX)
- File hashing
- Save/load functionality

#### `cli.py`
- Command handlers for all CLI commands
- User-friendly output formatting
- Error handling and messages
- Commands:
  - `init` - initialize CodeAtlas
  - `scan` - perform repository analysis
  - `map` - generate repository structure
  - `find` - search for symbols
  - `stats` - show statistics
  - `help` - display help

#### `scanner.py`
- **Scanner** class - repository analysis
- File discovery
- Language detection
- Directory traversal
- Excluded pattern matching
- Parser coordination
- Statistics collection

#### `parsers/java_parser.py`
- **JavaParser** class - Java code parsing
- Tree-sitter integration
- Class extraction
- Method extraction
- Dependency extraction
- Handles syntax errors gracefully

## Data Model

### Index Structure (.codeatlas/)

```
.codeatlas/
├── config.json          Project metadata
├── files.json           File inventory
├── symbols.json         Classes, methods, interfaces
├── dependencies.json    Relationships
└── graph.json          Full NetworkX graph
```

### config.json
```json
{
  "project_name": "MyProject",
  "author": "Dhiraj Pawar",
  "version": "1.0",
  "created": "2026-08-25T...",
  "last_updated": "2026-08-25T...",
  "languages": ["java"],
  "excluded_patterns": ["target", "build", ".git"]
}
```

### symbols.json
```json
{
  "class:MyClass:WebService": {
    "id": "class:MyClass:WebService",
    "name": "WebService",
    "type": "class",
    "file": "src/main/java/MyClass.java",
    "line": 10,
    "metadata": {...}
  }
}
```

### dependencies.json
```json
[
  ["class:A", "class:B", "uses"],
  ["class:B", "class:C", "imports"],
  ...
]
```

### graph.json
NetworkX node-link JSON format:
```json
{
  "nodes": [
    {"id": "class:A", "label": "ClassA", "type": "class"},
    ...
  ],
  "edges": [
    {"source": "class:A", "target": "class:B", "relation": "uses"},
    ...
  ]
}
```

## Parsing Pipeline

### Java Parsing (Current Implementation)

```
Java File
    ↓
Tree-Sitter AST
    ↓
Walk Tree Nodes
    ↓
├── Identify class declarations
├── Identify method declarations
└── Extract imports
    ↓
Extract Symbols
    ├── Class name, location, metadata
    └── Method name, location, metadata
    ↓
Extract Dependencies
    ├── Import relationships
    ├── Inheritance relationships
    └── Usage relationships
    ↓
Add to Index
    ├── Add nodes to graph
    └── Add edges to graph
```

## Symbol Naming Convention

Symbols are uniquely identified using a hierarchical ID scheme:

```
class:<filename>:<classname>
method:<filename>:<methodname>
interface:<filename>:<interfacename>
```

This ensures:
- Uniqueness across the repository
- Deterministic ID generation
- Human-readable debugging

## Dependency Types

Supported relationships:

- `imports` - Import statement
- `extends` - Class inheritance
- `implements` - Interface implementation
- `uses` - Class dependency/usage
- `calls` - Method call relationship
- `contains` - Nested class relationship
- `references` - General reference

## File Hashing

Each indexed file includes a SHA256 hash for:
- Change detection
- Incremental updates (Phase 3)
- Cache validation

```python
hash = SHA256(file_content)
```

## Language Support Architecture

Pluggable parser system:

```
Parser Interface
    ↓
    ├── JavaParser
    ├── PythonParser (future)
    ├── TypeScriptParser (future)
    └── ...
```

Each parser implements:
```python
def parse(path: Path, root: Path) -> Tuple[classes, methods, dependencies]
```

## Dependency Resolution

Two-phase extraction:

1. **AST-based** - Direct relationships from import statements
2. **Usage-based** - Derived relationships from method calls and class usage (future Phase 2)

## Performance Characteristics

| Operation | Time | Notes |
|-----------|------|-------|
| File discovery | O(n) | n = number of files |
| Parsing | O(f*s) | f = number of files, s = average file size |
| Indexing | O(s*e) | s = number of symbols, e = relationships |
| Lookup | O(1) | Hash table access |
| Graph traversal | O(v+e) | NetworkX graph operations |

## Storage Format

All files are JSON for:
- Easy inspection
- Deterministic output
- Simple debugging
- Integration with other tools
- No binary dependencies

## Testing Strategy

### Unit Tests
- Core functionality
- Index operations
- Parser correctness
- CLI commands

### Integration Tests
- End-to-end scanning
- Real repository analysis
- Index persistence

### Fixtures
- Sample Java files
- Mock repositories
- Known expected outputs

## Security Considerations

CodeAtlas:
- ✅ Never executes code
- ✅ Never makes network calls
- ✅ Never stores credentials
- ✅ Stores only structural metadata
- ✅ Uses relative paths
- ✅ Respects .gitignore and .codeatlasignore

## Future Phases

### Phase 2: Enhanced Analysis
- Method call relationships
- Inheritance chain analysis
- `class` command
- `method` command
- `impact` command
- `trace` command

### Phase 3: Incremental Updates
- File change detection
- Differential indexing
- `update` command
- `diff` command

### Phase 4: AI Context
- Minimal context generation
- Token budget support
- `context` command with options
- Token estimation

### Phase 5: Integration
- Claude skill generation
- MCP adapter (optional)
- Workflow optimization

### Phase 6: Expansion
- Multi-language support
- Advanced analytics
- Performance optimization
- Graph visualization

## Extensibility

CodeAtlas is designed for extension:

1. **Add new parser** - Implement `Parser` interface in `parsers/` directory
2. **Add new command** - Add handler in `cli.py` and route in `__main__.py`
3. **Add new exporter** - Implement exporter in `exporters/` directory
4. **Add new analysis** - Add module in root and integrate with index

## Known Limitations

### Current (Phase 1)
- Java only (other languages in Phase 6)
- No incremental updates (Phase 3)
- No AI-optimized context (Phase 4)
- No method-level call tracking (Phase 2)
- No GUI/web interface

### By Design
- Completely offline (no remote data)
- No AI models (deterministic analysis only)
- No vector embeddings
- No semantic analysis

## Error Handling

CodeAtlas handles common errors gracefully:

```
Parse Error
  ↓
Log warning with filename
  ↓
Continue with next file
  ↓
Mark file as unparseable
  ↓
Complete scan with partial results
```

No full scan fails due to single file error.

## Configuration

Users can configure:
- Project name and author
- Supported languages
- Excluded patterns
- Index location (future)
- Token budget (Phase 4)

Configuration is stored in `.codeatlas/config.json` and persists across runs.

---

**CodeAtlas Architecture** - Designed for precision, speed, and offline operation.
