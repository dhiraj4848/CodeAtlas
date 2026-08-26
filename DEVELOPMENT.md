# CodeAtlas Development Guide

## Getting Started

### Setup

```bash
git clone https://github.com/dhiraj-pawar/CodeAtlas
cd CodeAtlas
uv sync
```

### Verify Installation

```bash
uv run CodeAtlas --version
uv run pytest tests/ -q
```

## Running Locally

### Initialize and Scan

```bash
# Create a test repository
mkdir -p test-repo
cd test-repo

# Initialize CodeAtlas
uv run CodeAtlas init

# Answer the prompts:
# Project name: TestProject
# Author: Dhiraj Pawar

# Scan the repository
uv run CodeAtlas scan

# View results
uv run CodeAtlas map
uv run CodeAtlas stats
```

### CLI Commands

```bash
# Show version
uv run CodeAtlas --version

# Show help
uv run CodeAtlas help

# Initialize in current directory
uv run CodeAtlas init

# Perform complete scan
uv run CodeAtlas scan

# View repository map
uv run CodeAtlas map

# Find a class
uv run CodeAtlas find MyClass

# Find a method
uv run CodeAtlas find myMethod --type method

# Show statistics
uv run CodeAtlas stats
```

## Running Tests

### All Tests

```bash
uv run pytest tests/ -v
```

### Single Test File

```bash
uv run pytest tests/test_core.py -v
```

### Single Test

```bash
uv run pytest tests/test_core.py::test_codeatlas_init -v
```

### With Coverage

```bash
uv run pytest tests/ --cov=codeatlas --cov-report=html
open htmlcov/index.html
```

### Watch Mode (requires pytest-watch)

```bash
uv run pytest-watch tests/
```

## Code Style

### Formatting

```bash
# Check code style
uv run ruff check codeatlas/ tests/

# Format code (Ruff auto-format)
uv run ruff format codeatlas/ tests/
```

### Type Checking

```bash
# Check types (basic mode)
uv run pyright
```

### Pre-commit Hooks (Optional)

```bash
uv run pre-commit install
uv run pre-commit run --all-files
```

## Project Structure

```
CodeAtlas/
├── codeatlas/              Main package
│   ├── __init__.py
│   ├── __main__.py         CLI entry point
│   ├── cli.py              Command handlers
│   ├── core.py             Core engine
│   ├── scanner.py          Repository scanner
│   ├── parsers/            Language parsers
│   │   ├── __init__.py
│   │   └── java_parser.py
│   └── exporters/          Output exporters
│       └── __init__.py
│
├── tests/                  Test suite
│   ├── __init__.py
│   ├── conftest.py         Pytest fixtures
│   ├── test_core.py        Core tests
│   ├── fixtures/           Test data
│   │   └── sample.java
│   └── ...
│
├── docs/                   Documentation
├── pyproject.toml          Package metadata
├── README.md               User guide
├── CLAUDE.md              Developer guide
├── ARCHITECTURE.md         Architecture docs
├── DEVELOPMENT.md          This file
└── LICENSE                Apache 2.0
```

## Adding a Feature

### 1. Create Feature Branch

```bash
git checkout -b feature/my-feature
```

### 2. Write Tests First

Create test file: `tests/test_my_feature.py`

```python
def test_new_feature():
    # Arrange
    # Act
    # Assert
```

### 3. Implement Feature

- Create module in appropriate location
- Follow existing patterns
- Add docstrings
- No comments unless WHY is non-obvious

### 4. Run Tests

```bash
uv run pytest tests/test_my_feature.py -v
uv run pytest tests/ -q  # All tests
```

### 5. Check Code Style

```bash
uv run ruff check codeatlas/
uv run pyright
```

### 6. Commit

```bash
git add .
git commit -m "feat: my new feature"
```

### 7. Push and Create PR

```bash
git push origin feature/my-feature
# Create PR on GitHub
```

## Adding a New Language

### 1. Create Parser

Create `codeatlas/parsers/<lang>_parser.py`:

```python
class PythonParser:
    def parse(self, path: Path, root: Path) -> Tuple[List[dict], List[dict], List[Tuple]]:
        """Parse Python file and extract symbols."""
        # Implementation
```

### 2. Register Language

Update `codeatlas/scanner.py`:

```python
LANGUAGE_EXTENSIONS = {
    ".java": "java",
    ".py": "python",  # Add this
}
```

### 3. Add Tests

Create test fixture: `tests/fixtures/sample.py`

Add tests: `tests/test_parser.py`

```python
def test_python_parser(sample_python_file):
    parser = PythonParser()
    classes, methods, deps = parser.parse(...)
    # Assertions
```

### 4. Update Documentation

- Update README.md supported languages
- Update ARCHITECTURE.md parser list

## Common Tasks

### Debugging

Print diagnostic info:

```python
import json

# Print metadata
print(json.dumps(atlas.metadata.__dict__, indent=2))

# Print symbols
print(json.dumps(atlas.symbols_map, indent=2))

# Print graph
print(f"Nodes: {atlas.graph.number_of_nodes()}")
print(f"Edges: {atlas.graph.number_of_edges()}")
```

### Testing with Real Repository

```bash
cd /path/to/real/java/project
uv run CodeAtlas init
uv run CodeAtlas scan --verbose
uv run CodeAtlas stats
```

### Performance Profiling

```bash
# Time the scan
time uv run CodeAtlas scan

# Profile with cProfile
python -m cProfile -s cumtime -m codeatlas.__main__ scan
```

### Inspecting Generated Index

```bash
# View configuration
cat .codeatlas/config.json | jq .

# Count symbols
cat .codeatlas/symbols.json | jq 'length'

# View graph edges
cat .codeatlas/graph.json | jq '.edges | length'
```

## CI/CD

### GitHub Actions

Workflows in `.github/workflows/`:

- `test.yml` - Run tests on push
- `lint.yml` - Run linting
- `type-check.yml` - Run type checking

### Local CI Simulation

```bash
# Run all CI checks locally
uv run pytest tests/ -q
uv run ruff check codeatlas/ tests/
uv run pyright
```

## Documentation

### Updating README

- Keep it concise
- Include examples
- Update table of contents
- Test all code snippets

### Updating CLAUDE.md

- Explain architecture
- Document conventions
- Provide guidelines
- Keep current

### Updating ARCHITECTURE.md

- Explain design decisions
- Include diagrams
- Document data model
- List limitations

## Version Management

Current version: `0.1.0` in `pyproject.toml`

Bump with:

```bash
# Patch release (bug fixes)
# 0.1.0 -> 0.1.1
sed -i 's/version = "0.1.0"/version = "0.1.1"/' pyproject.toml

# Minor release (new features)
# 0.1.0 -> 0.2.0
sed -i 's/version = "0.1.0"/version = "0.2.0"/' pyproject.toml
```

Then tag release:

```bash
git tag v0.1.1
git push origin v0.1.1
```

## Release Process

1. Update version in `pyproject.toml`
2. Update `CHANGELOG.md`
3. Update `README.md` if needed
4. Run full test suite
5. Commit and tag
6. Push to main
7. Create GitHub Release

## Troubleshooting

### "ModuleNotFoundError: No module named 'codeatlas'"

```bash
uv sync --reinstall
```

### Tests fail with "IndexError"

- Check test fixtures exist
- Verify temp directory permissions
- Check for stale .pyc files

### Parser crashes on file

- Check file encoding (must be UTF-8)
- Check file is valid syntax
- Test with sample files first

### Graph has no edges

- Verify imports are being parsed
- Check dependency extraction logic
- Test with known dependency files

## Getting Help

- Check README.md
- Review CLAUDE.md
- Look at existing tests
- Check ARCHITECTURE.md
- Open a GitHub issue

## Performance Tips

### Scanning Large Repositories

```bash
# Use parallel processing (future feature)
CODEATLAS_MAX_WORKERS=8 uv run CodeAtlas scan

# Limit languages
CODEATLAS_LANGUAGES=java uv run CodeAtlas scan
```

### Querying Index

```bash
# Use graph filtering
# Query complexity is O(V+E) in NetworkX

# Keep queries focused
uv run CodeAtlas find MyClass  # O(n) where n = symbols
```

## Future Work

See `README.md` and `ARCHITECTURE.md` for Phase 2-6 roadmap.

Current priorities:
1. Phase 2: Enhanced analysis (impact, trace)
2. Phase 3: Incremental updates
3. Phase 4: AI context optimization

---

**CodeAtlas Development Guide** - Build, test, and extend CodeAtlas.
