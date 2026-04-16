# Agent Guidelines for autocommit

This file provides guidelines for AI agents working on the autocommit codebase.

## Project Overview

- **Type**: Python CLI tool (single executable script)
- **Purpose**: Git Auto Committer using Ollama AI models
- **Language**: Python 3.6+
- **Main File**: `autocommit` (1533 lines)

## Build, Lint, and Test Commands

### Installation

```bash
pip install -e .
pip install -e ".[dev]"
```

### Running the tool

```bash
./autocommit                    # Basic usage
./autocommit -y              # Auto-accept mode
./autocommit --model qwen2.5-coder:7b-instruct
./autocommit --tag v1.2.6 --release
```

### Running Tests

```bash
pytest tests/                 # Run all tests
pytest tests/ -v           # Verbose output
pytest tests/ -k "test_name" # Run specific test
python -m pytest tests/ -x  # Stop on first failure
```

### Running a Single Test

```bash
pytest tests/test_filename.py::test_function_name -v
```

### Linting

```bash
flake8 autocommit            # Lint main script
flake8 autocommit --max-line-length=100
```

### Type Checking

```bash
mypy autocommit
mypy autocommit --strict
```

### Code Formatting

```bash
black autocommit             # Format code
black autocommit --check   # Check without modifying
```

## Code Style Guidelines

### Imports

- Standard library imports first: `os`, `sys`, `subprocess`, `json`, `argparse`, `pathlib`, `typing`, `requests`, `datetime`, `fnmatch`
- Grouped by: stdlib, third-party
- No `from x import *` - use explicit imports
- Sort imports alphabetically within groups

### Formatting

- Line length: 100 characters max
- Indentation: 4 spaces (no tabs)
- Use f-strings for string formatting
- Use docstrings for function documentation (single line for simple functions)
- No trailing whitespace

### Types

- Use type hints for function parameters and return values
- Use `Optional[X]` instead of `X | None` (Python 3.6 compatibility)
- Use `Tuple[X, Y]` for multi-return functions
- Primitive types: `str`, `int`, `bool`, `float`
- Collections: `List[X]`, `Set[X]`, `Dict[X, Y]`

Example:
```python
def run_git_command(self, command: List[str]) -> Tuple[bool, str]:
```

### Naming Conventions

- Classes: `PascalCase` (e.g., `GitAutoCommitter`, `Colors`)
- Functions/methods: `snake_case` (e.g., `get_git_status`)
- Variables: `snake_case` (e.g., `git_dir`)
- Constants: `UPPER_SNAKE_CASE` (e.g., `DEFAULT_MODEL`)
- Private methods: prefix with `_` (e.g., `_find_git_root`)

### Error Handling

- Never fail silently - always log errors with `log_error()` or `die()`
- Use `try/except` blocks for external calls (git, requests, file I/O)
- Return error details in tuple form: `(False, "error message")`
- Use success pattern consistently: return `(bool, str)` or `(bool, data)`
- Handle specific exceptions first: `json.JSONDecodeError`, `subprocess.CalledProcessError`, etc.
- Catch `FileNotFoundError` for missing executables

Example:
```python
try:
    result = subprocess.run(...)
    return True, result.stdout.strip()
except subprocess.CalledProcessError as e:
    return False, f"{e.stderr.strip()}\nExit code: {e.returncode}"
```

### Logging Functions

Use provided logging helpers:
- `log_info(message)` - general info
- `log_success(message)` - success with checkmark
- `log_warning(message)` - warnings
- `log_error(message)` - errors
- `die(message)` - fatal error (exits with code 1)
- `show_header(title)` - section headers
- `show_section(title)` - subsection headers

### Class Structure

```python
class GitAutoCommitter:
    def __init__(self, model: str = None, ollama_url: str = DEFAULT_OLLAMA_URL):
        self.model = model or get_default_model()
        self.ollama_url = ollama_url
        self.git_dir = self.find_git_root()
    
    def find_git_root(self) -> Optional[Path]:
        """Find the git repository root directory."""
        # Implementation
```

### Git Command Pattern

Always use the success/error tuple pattern:
```python
def run_git_command(self, command: List[str]) -> Tuple[bool, str]:
    try:
        result = subprocess.run(
            ["git"] + command,
            cwd=self.git_dir,
            capture_output=True,
            text=True,
            check=True
        )
        return True, result.stdout.strip()
    except subprocess.CalledProcessError as e:
        return False, f"{e.stderr.strip()}\nExit code: {e.returncode}"
```

### API Calls

For Ollama API:
```python
try:
    response = requests.post(
        f"{self.ollama_url}/api/chat",
        json={
            "model": self.model,
            "messages": [...],
            "stream": False,
            "options": {"temperature": 0.1, "num_predict": 200}
        },
        timeout=120
    )
    response.raise_for_status()
    result = response.json()
    # Process result
except requests.exceptions.RequestException as e:
    log_error(f"Failed to connect to Ollama: {e}")
    return None
```

### Configuration

- Store user config in `~/.config/autocommit/config.json`
- Use `Path.home() / ".config" / "autocommit" / "config.json"`
- Handle malformed config gracefully with try/except

### Exit Codes

- `0` - success
- `1` - failure/error
- Use `sys.exit(0)` and `sys.exit(1)` in `main()`

### Color Output

Use the `Colors` class for terminal output:
```python
class Colors:
    RED = '\033[0;31m'
    GREEN = '\033[0;32m'
    YELLOW = '\033[1;33m'
    BLUE = '\033[0;34m'
    CYAN = '\033[0;36m'
    MAGENTA = '\033[0;35m'
    WHITE = '\033[1;37m'
    GRAY = '\033[0;90m'
    BOLD = '\033[1m'
    DIM = '\033[2m'
    NC = '\033[0m'  # No Color
```

### File Patterns

- Use `fnmatch` for glob-style matching
- Support `.autocommit-ignore` files (similar syntax to `.gitignore`)
- Handle directory patterns (ending with `/`), wildcards, and exact matches

### No External Rules Found

- No `.cursor/rules/` or `.cursorrules` files
- No `.github/copilot-instructions.md`
- No existing `AGENTS.md`

## Key Files

- `autocommit` - Main executable (1533 lines)
- `pyproject.toml` - Project configuration
- `README.md` - Documentation
- `CHANGELOG.md` - Version history