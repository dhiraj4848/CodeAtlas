# CodeAtlas v0.3.0

## Offline Repository Intelligence & AI-Context Optimization Tool

**Reduce AI token consumption by eliminating unnecessary repository exploration.**

CodeAtlas analyzes your repository once and provides precise file, class, and method discovery—enabling AI agents like Claude to make informed decisions without wasteful repository scanning.

---

## Table of Contents

1. [What is CodeAtlas?](#what-is-codeatlas)
2. [Why CodeAtlas?](#why-codeatlas)
3. [Installation](#installation)
4. [Quick Start](#quick-start)
5. [Configuration](#configuration)
6. [Commands Reference](#commands-reference)
7. [Use Cases](#use-cases)
8. [Architecture](#architecture)
9. [Troubleshooting](#troubleshooting)
10. [FAQ](#faq)

---

## What is CodeAtlas?

CodeAtlas is a **local-first, offline repository intelligence tool** that:

- ✅ Analyzes repositories completely offline (no APIs, no internet required)
- ✅ Extracts code structure (classes, methods, dependencies)
- ✅ Builds searchable index for instant lookups
- ✅ Provides 13 powerful commands for code exploration
- ✅ Helps AI agents understand code context efficiently
- ✅ Tracks changes incrementally for fast updates

**Current Phase**: 3 of 6 (Complete through incremental updates)
**Supported Languages**: Java (primary), more in Phase 5
**Status**: Production-ready

---

## Why CodeAtlas?

### The Problem
When working with AI coding assistants like Claude, they often waste tokens on:
```
Discovery → Multiple searches → Reading unrelated files → Understanding
```

### The Solution
```
CodeAtlas Query → Precise Results → Read only relevant files → Understanding
```

### Token Savings Example
**Without CodeAtlas**:
- Search repository: 2,000 tokens
- Read 20+ files: 15,000 tokens
- Total: ~17,000 tokens wasted on discovery

**With CodeAtlas**:
- Query: 50 tokens
- Read 3 files: 2,000 tokens
- Total: ~2,050 tokens (88% reduction!)

---

## Installation

### Prerequisites

- **Python**: 3.10 or higher
- **Git**: For version control
- **Internet**: Only for initial installation (not required after)

### Step 1: Verify Python Installation

```bash
python --version
# Should output: Python 3.10.x or higher

python3 --version
# On macOS/Linux, may need to use python3
```

### Step 2: Install uv (Package Manager - Recommended)

**Windows**:
```powershell
# Using Windows Package Manager
winget install astral-sh.uv

# Or using PowerShell
irm https://astral.sh/uv/install.ps1 | iex
```

**macOS**:
```bash
# Using Homebrew
brew install uv

# Or using curl
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Linux**:
```bash
# Using curl
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Alternative: Use pip**:
```bash
pip install pipx
pipx install uv
```

### Step 3: Clone CodeAtlas Repository

```bash
# Clone the repository
git clone https://github.com/dhiraj-pawar/CodeAtlas.git

# Navigate to directory
cd CodeAtlas

# Verify you're on the right branch
git status
```

### Step 4: Install Dependencies

```bash
# Using uv (recommended)
uv sync

# Using pip alternative
pip install -e .
```

### Step 5: Verify Installation

```bash
# Check version
CodeAtlas --version
# Output: CodeAtlas 0.3.0

# Show help
CodeAtlas help
# Should display all commands

# Verify Python module
python -c "import codeatlas; print(codeatlas.__file__)"
```

---

## Quick Start (5 Minutes)

### On Your First Repository

```bash
# 1. Navigate to your project
cd /path/to/your/java/project

# 2. Initialize CodeAtlas
CodeAtlas init
# Answer prompts:
# - Project name: [MyProject]
# - Author name: [Dhiraj Pawar]

# 3. Scan the repository (first time)
CodeAtlas scan
# This creates .codeatlas/ directory with index

# 4. View repository structure
CodeAtlas map

# 5. Show statistics
CodeAtlas stats

# 6. Find a class
CodeAtlas find MyClass

# 7. Get class details
CodeAtlas class MyClass

# 8. Check what's affected by changes
CodeAtlas impact MyClass

# 9. Trace dependencies
CodeAtlas trace ClassA ClassB

# 10. Update (next time code changes)
CodeAtlas update
```

---

## Configuration

### What Gets Created

When you run `CodeAtlas init`, this directory structure is created:

```
.codeatlas/
├── config.json              # Project configuration
├── files.json               # File inventory
├── symbols.json             # Classes, methods, interfaces
├── dependencies.json        # Relationships between symbols
├── graph.json              # Full NetworkX graph
└── hashes.json             # File hashes for change detection
```

### Configuration File (.codeatlas/config.json)

```json
{
  "project_name": "MyProject",
  "author": "Dhiraj Pawar",
  "version": "1.0",
  "created": "2026-08-25T10:30:00",
  "last_updated": "2026-08-25T10:35:00",
  "languages": ["java"],
  "excluded_patterns": [
    "target",
    "build",
    ".git",
    "node_modules"
  ]
}
```

### Excluding Files

Create `.codeatlasignore` in your project root:

```
# .codeatlasignore
target/
build/
dist/
node_modules/
*.tmp
*.log
.git/
.idea/

# Only scan src directory
*
!src/
!src/**
```

**Rules**:
- Same syntax as `.gitignore`
- Patterns are merged with `.gitignore`
- `.gitignore` is respected automatically
- Use `!` for negation

### Committing to Git

The `.codeatlas/` directory is safe to commit:

```bash
# Add to git
git add .codeatlas/

# In .gitignore, optionally exclude cost tracking
echo "
# CodeAtlas
.codeatlas/cost.json
.codeatlas/usage/
" >> .gitignore
```

**Benefits**:
- Team starts with ready-made index
- No rebuild needed on first checkout
- Everyone has consistent analysis

---

## Commands Reference

### Phase 1: Discovery & Indexing

#### `CodeAtlas init`

**Description**: Initialize CodeAtlas in current repository.

**Usage**:
```bash
CodeAtlas init
```

**What it does**:
- Creates `.codeatlas/` directory
- Creates `config.json` with project metadata
- Sets up configuration for scanning

**Example**:
```bash
$ CodeAtlas init
CodeAtlas Initialization
==================================================
Project name [MyProject]: MyJavaApp
Author name [Dhiraj Pawar]: John Doe

✓ CodeAtlas initialized successfully!
✓ Configuration saved to: .codeatlas/config.json

Next steps:
  1. Run: CodeAtlas scan
  2. Run: CodeAtlas map
  3. Run: CodeAtlas find <ClassName>
```

**Notes**:
- Only needs to be run once per project
- Interactive prompts for configuration
- Creates no changes to source code

---

#### `CodeAtlas scan`

**Description**: Perform complete repository analysis (first full scan).

**Usage**:
```bash
CodeAtlas scan
```

**What it does**:
- Discovers all source files
- Extracts symbols (classes, methods)
- Builds dependency graph
- Creates searchable index
- Takes a few seconds for typical repos

**Example Output**:
```bash
$ CodeAtlas scan

CodeAtlas
--------------------------------------------------
Scanning repository...

Files discovered       : 142
Files analyzed         : 142
Classes                : 45
Methods                : 312
Dependencies           : 287

Repository graph built successfully.
Index location: .codeatlas/
```

**Time Complexity**:
- 100 files: <500ms
- 1,000 files: 2-5 seconds
- 10,000 files: 10-30 seconds

**Notes**:
- Only needed once for initial indexing
- Use `CodeAtlas update` for subsequent scans
- Can be run from any directory

---

#### `CodeAtlas map`

**Description**: Generate visual repository structure map.

**Usage**:
```bash
CodeAtlas map
```

**What it does**:
- Shows organized list of classes
- Helps understand repository structure
- Displays first 20 classes (summary)

**Example Output**:
```bash
$ CodeAtlas map

Repository Structure
==================================================
  ApplicationConfig
  AuthFilter
  BaseController
  CacheManager
  DatabaseConnection
  DTO
  EmailService
  FileProcessor
  ... and 37 more classes
```

**Use Cases**:
- Understand codebase overview
- Find entry points
- Identify main components

---

#### `CodeAtlas find <QUERY>`

**Description**: Search for symbols by name (classes, methods, files).

**Usage**:
```bash
CodeAtlas find <name>               # Find any symbol
CodeAtlas find <name> --type class  # Find only classes
CodeAtlas find <name> --type method # Find only methods
CodeAtlas find <name> --type file   # Find only files
```

**Options**:
- `--type`: Filter by type (class, method, file)

**Examples**:
```bash
# Find any symbol with "Service" in name
$ CodeAtlas find Service
CodeAtlas Search Results
==================================================

Match: WebServiceDialog
  Type:   class
  File:   src/main/java/WebServiceDialog.java
  Line:   42

Match: WebService
  Type:   class
  File:   src/main/java/WebService.java
  Line:   10

Match: save
  Type:   method
  File:   src/main/java/Service.java
  Line:   25

# Find only classes
$ CodeAtlas find Dialog --type class

# Find only methods named "save"
$ CodeAtlas find save --type method
```

**Use Cases**:
- Locate a specific class
- Find all methods with same name
- Discover related components

---

#### `CodeAtlas stats`

**Description**: Show comprehensive repository statistics.

**Usage**:
```bash
CodeAtlas stats
```

**What it does**:
- Counts symbols (classes, methods, interfaces)
- Shows dependency count
- Displays project metadata
- Gives code health overview

**Example Output**:
```bash
$ CodeAtlas stats

CodeAtlas Statistics
==================================================
Files          : 142
Classes        : 45
Methods        : 312
Total Symbols  : 389
Dependencies   : 287

Project: MyJavaApp
Author:  John Doe
Created: 2026-08-25T10:30:00
```

**Use Cases**:
- Understand project scale
- Track growth over time
- Quick repository overview

---

### Phase 2: Advanced Analysis

#### `CodeAtlas class <NAME>`

**Description**: Show detailed information about a class and its dependencies.

**Usage**:
```bash
CodeAtlas class <ClassName>
```

**What it does**:
- Shows class location and line number
- Lists all classes it depends on
- Lists all classes that use it
- Shows connection statistics

**Example Output**:
```bash
$ CodeAtlas class WebServiceDialog

Class: WebServiceDialog
==================================================
File:          src/main/java/WebServiceDialog.java
Line:          42

Dependencies (3):
  - WebServiceDto
  - WebServicePresenter
  - Logger

Used by (2):
  - MainApplication
  - ViewFactory

Connections:
  In-degree:     2
  Out-degree:    3
```

**Use Cases**:
- Understand class structure
- Find dependencies
- Identify who uses this class

---

#### `CodeAtlas method <NAME>`

**Description**: Show detailed information about a method and its relationships.

**Usage**:
```bash
CodeAtlas method <MethodName>
```

**What it does**:
- Shows method location and line
- Lists methods it calls
- Lists methods that call it
- Shows method connections

**Example Output**:
```bash
$ CodeAtlas method save

Method: save
==================================================
File:          src/main/java/Service.java
Line:          25

Calls (2):
  - Repository.find()
  - Logger.log()

Called by (3):
  - Controller.handleSave()
  - Dialog.save()
  - Validator.validate()
```

**Use Cases**:
- Trace method calls
- Find all callers
- Understand method impact

---

#### `CodeAtlas impact <NAME> [--depth N]`

**Description**: Show what's affected by changes to a symbol.

**Usage**:
```bash
CodeAtlas impact <Name>              # Default depth=2
CodeAtlas impact <Name> --depth 1    # Direct only
CodeAtlas impact <Name> --depth 3    # Deeper analysis
```

**Options**:
- `--depth`: How many levels to traverse (default: 2)

**What it does**:
- Shows direct dependents (things that use it)
- Shows indirect dependents (affected by change)
- Lists all potentially affected files
- Estimates change impact

**Example Output**:
```bash
$ CodeAtlas impact WebServiceService --depth 2

Impact Analysis: WebServiceService
==================================================

Direct Dependents (3):
  - WebServiceView
  - WebServiceDialog
  - WebServiceController

Indirect Dependents (5):
  - MainApplication
  - ApplicationConfig
  - RequestHandler
  - ResponseProcessor
  - ErrorLogger

Potential Affected Files (8):
  - src/.../WebServiceView.java
  - src/.../WebServiceDialog.java
  - src/.../WebServiceController.java
  - src/.../MainApplication.java
  - ...

Total Affected Symbols: 8
```

**Use Cases**:
- Estimate change scope before refactoring
- Plan testing strategy
- Understand code fragility

---

#### `CodeAtlas trace <SOURCE> <TARGET>`

**Description**: Show how two symbols are connected (dependency path).

**Usage**:
```bash
CodeAtlas trace <ClassA> <ClassB>
```

**What it does**:
- Finds shortest path between symbols
- Shows the connection chain
- Displays relationship types
- Helps understand dependencies

**Example Output**:
```bash
$ CodeAtlas trace WebServiceDialog WebServiceRepository

Dependency Trace
==================================================
From: WebServiceDialog
To:   WebServiceRepository

Path (4 nodes, 3 edges):
  1. WebServiceDialog
     ↓ [uses]
  2. WebServicePresenter
     ↓ [uses]
  3. WebServiceService
     ↓ [uses]
  4. WebServiceRepository

Steps: 3
```

**Use Cases**:
- Understand how components connect
- Find shortest path between classes
- Debug dependency issues
- Plan refactoring paths

---

### Phase 3: Maintenance & Updates

#### `CodeAtlas update`

**Description**: Perform fast incremental repository update (only changed files).

**Usage**:
```bash
CodeAtlas update
```

**What it does**:
- Detects file changes (added/modified/deleted)
- Only re-parses changed files
- Updates index incrementally
- Preserves unchanged data
- Much faster than full scan

**Example Output**:
```bash
$ CodeAtlas update

CodeAtlas Incremental Update
--------------------------------------------------
Detecting changes...

Repository Status:
  Files unchanged: 140
  Files modified:  2
  Files added:     1
  Files deleted:   0

Re-parsing 3 files...

Update Summary:
  Classes added:    1
  Classes modified: 0
  Classes deleted:  0
  Methods added:    3
  Methods modified: 0
  Methods deleted:  0

Index updated successfully.
```

**Performance**:
- No changes: <50ms
- Minor changes: <100ms
- Significant changes: 1-5 seconds

**Use Cases**:
- Run after code changes
- Update index before AI consultation
- Keep index synchronized

---

#### `CodeAtlas diff`

**Description**: Show what changed since last scan.

**Usage**:
```bash
CodeAtlas diff
```

**What it does**:
- Shows file changes (without updating)
- Lists added files
- Lists modified files
- Lists deleted files
- Doesn't modify index

**Example Output**:
```bash
$ CodeAtlas diff

CodeAtlas Change Summary
==================================================

Files:
  Unchanged:  140
  Modified:   2
  Added:      1
  Deleted:    0

Status: 3 file(s) changed

Run: CodeAtlas update
```

**Use Cases**:
- Check what changed before updating
- Review modifications
- Decide if update is needed

---

#### `CodeAtlas doctor`

**Description**: Check index integrity and health.

**Usage**:
```bash
CodeAtlas doctor
```

**What it does**:
- Validates index structure
- Checks for circular dependencies
- Detects isolated symbols
- Verifies graph consistency
- Reports any issues

**Example Output**:
```bash
$ CodeAtlas doctor

CodeAtlas Index Doctor
==================================================

Health Checks:
  Index....................... OK
  Graph....................... OK
  Files....................... OK
  Circular deps............... OK
  Isolated symbols............ WARNING: 2

Statistics:
  Symbols:        389
  Classes:        45
  Methods:        312
  Edges:          287
  Avg degree:     1.47

✓ Index is healthy
```

**Use Cases**:
- Verify index after issues
- Troubleshoot problems
- Monitor index quality

---

#### `CodeAtlas help`

**Description**: Display help information.

**Usage**:
```bash
CodeAtlas help
```

**Shows**:
- All available commands
- Command descriptions
- Usage examples
- Available options

---

## Use Cases

### Use Case 1: Understand a New Component

**Goal**: Understand how `WebServiceDialog` works and what it depends on.

**Steps**:
```bash
# 1. Find the class
CodeAtlas find WebServiceDialog

# 2. Get class details
CodeAtlas class WebServiceDialog

# 3. See what it depends on
CodeAtlas impact WebServiceDialog

# 4. Trace to database layer
CodeAtlas trace WebServiceDialog Repository
```

**Result**: Complete understanding of component with 4 commands instead of 30 minutes of code reading.

---

### Use Case 2: Plan a Refactoring

**Goal**: Refactor `AuthService` safely—need to know impact.

**Steps**:
```bash
# 1. Check what uses AuthService
CodeAtlas impact AuthService --depth 3

# 2. See which files are affected
CodeAtlas diff

# 3. Plan testing (test affected files)
# 4. Update index after changes
CodeAtlas update

# 5. Verify health
CodeAtlas doctor
```

**Result**: Confident refactoring with clear impact analysis.

---

### Use Case 3: Onboard New Team Member

**Goal**: Help new developer understand codebase quickly.

**Steps**:
```bash
# 1. Show repository structure
CodeAtlas map

# 2. Show statistics
CodeAtlas stats

# 3. Answer specific questions
CodeAtlas find UserController
CodeAtlas class UserController
CodeAtlas impact UserController
```

**Result**: New developer productive in 1 hour instead of 1 week.

---

### Use Case 4: AI Code Generation

**Goal**: Generate code for new feature with AI assistance (Claude).

**Steps**:
```bash
# 1. Scan repository
CodeAtlas scan

# 2. In Claude: Use CodeAtlas findings
#    "Here's what connects to UserService:
#     - UserController (calls it)
#     - UserRepository (it uses)
#     - UserDto (passes to repository)
#     Generate the new feature using this pattern"

# 3. After implementation
CodeAtlas update

# 4. Verify
CodeAtlas doctor
```

**Result**: AI generates code matching existing patterns—90% less review needed.

---

## Architecture

### Data Flow

```
Source Code
    ↓
File Scanner (detects .java files)
    ↓
Java Parser (extracts classes, methods)
    ↓
Index Builder (creates graph)
    ↓
.codeatlas/ (persists to disk)
    ↓
CLI Queries (CodeAtlas find, class, etc.)
    ↓
Results to User / AI Agent
```

### Storage Structure

```
.codeatlas/
├── config.json
│   └── Project metadata, languages, excludes
│
├── files.json
│   └── File inventory (path, type, hash, size)
│
├── symbols.json
│   └── All symbols (classes, methods, interfaces)
│   └── { id, name, type, file, line, metadata }
│
├── dependencies.json
│   └── All relationships between symbols
│   └── [source, target, relation_type]
│
├── graph.json
│   └── Full NetworkX graph (JSON format)
│   └── Nodes + edges
│
└── hashes.json
    └── File hashes for change detection
```

### Technology Stack

| Component | Technology |
|-----------|-----------|
| Language | Python 3.10+ |
| CLI | argparse |
| Code Parsing | tree-sitter |
| Graphs | NetworkX |
| Storage | JSON |
| Testing | pytest |
| Package Manager | uv (or pip) |

---

## Troubleshooting

### Issue: "CodeAtlas: command not found"

**Problem**: Command not available after installation.

**Solution**:
```bash
# Option 1: Use with uv
uv run CodeAtlas --help

# Option 2: Add to PATH
# For uv:
uv tool update-shell

# For pip:
export PATH="$PATH:$HOME/.local/bin"

# Option 3: Run as module
python -m codeatlas.__main__ --help
```

---

### Issue: "CodeAtlas is not initialized"

**Problem**: Getting error when running commands.

**Solution**:
```bash
# Initialize the repository
CodeAtlas init

# Answer the prompts
# Then run your command
```

---

### Issue: "No index found"

**Problem**: Commands say "No index found. Run: CodeAtlas scan"

**Solution**:
```bash
# Perform initial scan
CodeAtlas scan

# This creates .codeatlas/ directory
# Now you can use all commands
```

---

### Issue: Index has fewer nodes after update

**Problem**: Index size decreased after `CodeAtlas update`

**Solution**:
```bash
# This is normal if you:
# - Deleted files
# - Renamed classes
# - Removed methods

# Force full rescan if needed:
CodeAtlas scan --force
```

---

### Issue: Parser fails on some files

**Problem**: See warnings about files that couldn't be parsed

**Solution**:
```bash
# Check file encoding (must be UTF-8)
# - Fix encoding if needed

# Check Java syntax
# - Ensure all files are valid Java

# Some advanced Java features may not parse
# - This is logged but doesn't stop the scan
# - Index will be built from parseable files

# Run again
CodeAtlas scan
```

---

### Issue: Large repository takes too long

**Problem**: Scanning takes several minutes

**Solution**:
```bash
# For first scan - this is normal, do once

# Next time - use incremental update
CodeAtlas update

# To speed up:
# - Exclude unnecessary directories in .codeatlasignore
# - Use CodeAtlas update instead of rescan
```

---

## FAQ

### Q: Do I need internet to use CodeAtlas?

**A**: No. CodeAtlas is completely offline.
- Indexing: 100% local
- Queries: 100% local
- Updates: 100% local
- No API calls, no remote services

### Q: Can I commit `.codeatlas/` to git?

**A**: Yes, and recommended!
- Makes index portable
- Team gets ready-made index
- No rebuild on first checkout
- Everyone has same index

### Q: How often should I run `CodeAtlas update`?

**A**: After significant code changes:
- After feature development
- Before consulting AI
- Before planning refactoring
- Regularly during development

### Q: What programming languages does CodeAtlas support?

**A**: Currently:
- **Phase 3 (now)**: Java
- **Phase 5 (planned)**: Python, TypeScript, JavaScript, Go, Rust
- **Easy to extend**: Pluggable parser architecture

### Q: How much disk space does the index take?

**A**: Very small:
- Typical project: 2-10 MB
- Large project: 50-100 MB
- Only stores structure, not code

### Q: Can I use CodeAtlas with my AI assistant?

**A**: Yes! Perfect for:
- Claude Code
- GitHub Copilot
- Cursor
- Any AI agent that can read files

**Workflow**:
```
1. CodeAtlas scan (in repo)
2. Share findings with AI
3. AI uses precise context
4. Get better code suggestions
```

### Q: How accurate is the dependency analysis?

**A**: Very accurate:
- AST-based extraction (explicit)
- Tracks imports, method calls, class usage
- NetworkX graph for traversal
- Some inferred relationships may vary

### Q: Can I exclude certain directories?

**A**: Yes, use `.codeatlasignore`:
```
target/
build/
node_modules/
.git/
*.tmp
```

Or in `CodeAtlas init` configuration.

### Q: What if I accidentally delete `.codeatlas/`?

**A**: Simply rescan:
```bash
CodeAtlas scan
```

Index will be rebuilt from source files.

### Q: Does CodeAtlas run code or execute anything?

**A**: No. It only:
- Reads files (never writes to them)
- Parses syntax (never executes)
- Builds index (never runs code)
- Completely safe

---

## Quick Reference Card

```bash
# SETUP
CodeAtlas init          Initialize project
CodeAtlas scan          Full index (first time)

# DISCOVERY
CodeAtlas find <name>   Search for symbols
CodeAtlas map           View structure
CodeAtlas stats         Show statistics

# ANALYSIS  
CodeAtlas class <name>  Class details
CodeAtlas method <name> Method details
CodeAtlas impact <name> What's affected
CodeAtlas trace <a><b>  Show connection

# MAINTENANCE
CodeAtlas update        Fast incremental scan
CodeAtlas diff          Show changes
CodeAtlas doctor        Health check

# HELP
CodeAtlas help          Show all commands
CodeAtlas --version     Show version
```

---

## Getting Help

### Resources

1. **README**: This file (detailed guide)
2. **CLAUDE.md**: Developer guide
3. **ARCHITECTURE.md**: Technical design
4. **PHASES_COMPLETE.md**: Implementation details

### Report Issues

GitHub Issues: https://github.com/dhiraj-pawar/CodeAtlas/issues

Include:
- CodeAtlas version: `CodeAtlas --version`
- Python version: `python --version`
- OS: Windows/macOS/Linux
- Command that failed
- Error message

---

## License

Apache 2.0 - See LICENSE file

---

## Author

**Dhiraj Pawar**

Created: 2026-08-25
Version: 0.3.0
Status: Production-Ready ✅

---

## What's Next?

CodeAtlas is complete through Phase 3. Future phases will add:
- **Phase 4**: AI context optimization, Claude skill
- **Phase 5**: Multi-language support
- **Phase 6**: Advanced features, web interface

---

**CodeAtlas** - Offline repository intelligence for AI-optimized development.

Get started: `CodeAtlas init && CodeAtlas scan`
