# Python Development Tools - Learning Plan

**Created:** 2026-03-12
**Duration:** 10-12 hours (hands-on practice)
**Goal:** Write production-ready Python projects with professional tools

---

##  Table of Contents

1. [Code Formatting](#1-code-formatting---black--ruff-format) - Black & Ruff
2. [Linting](#2-linting---ruff) - Ruff (faster than flake8/pylint)
3. [Import Sorting](#3-import-sorting---isort) - isort
4. [Type Checking](#4-type-checking---mypy) - mypy
5. [Testing](#5-testing---pytest) - pytest
6. [Code Coverage](#6-code-coverage---coverage--pytest-cov) - coverage
7. [Test Automation](#7-test-automation---tox) - tox
8. [Git Hooks](#8-git-hooks---pre-commit) - pre-commit
9. [Documentation](#9-documentation-optional) - docstrings, type hints
10. [Final Project](#10-final-project---complete-setup)

---

##  Why Are These Tools Important?

| Tool | What it does | Why you need it |
|------|--------------|-----------------|
| **black** | Code formatter | Consistent code style, no arguments |
| **ruff** | Linter (fast!) | Detect errors, code smells |
| **isort** | Import sorting | Clean import blocks |
| **mypy** | Type checker | Type safety, fewer bugs |
| **pytest** | Testing framework | Unit/integration tests |
| **coverage** | Code coverage | How much we test |
| **tox** | Test automation | Multi-Python versions, CI/CD |
| **pre-commit** | Git hooks | Automatic checks before commit |

---

##  Installation (Prerequisites)

```bash
# With uv (modern, recommended - you're already using it!)
uv pip install black ruff isort mypy pytest pytest-cov tox pre-commit

# Or with pip
pip install black ruff isort mypy pytest pytest-cov tox pre-commit
```

---

## 1. Code Formatting - Black & Ruff Format

### 1.1 Black - The Uncompromising Code Formatter

**Philosophy:** "You can have any color you want, as long as it's black."

**Installation:**
```bash
uv pip install black
```

**Exercise 1: Black basics**

Create an `ugly_code.py` file:

```python
# ugly_code.py - BAD formatting
def calculate(x,y,z):
    result=x+y+z
    if result>100:
        print( "Too big!" )
    else:
        print("OK")
    return result

class MyClass:
  def __init__(self,name,age):
      self.name=name
      self.age=age

  def greet(self):
        return f"Hello, {self.name}"

data=[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]
my_dict={"key1":"value1","key2":"value2","key3":"value3","key4":"value4"}
```

**Run:**

```bash
# Dry-run (preview only)
black --check ugly_code.py

# Format
black ugly_code.py

# Diff view
black --diff ugly_code.py

# Entire project
black .
```

**Result:**

```python
# ugly_code.py - After Black
def calculate(x, y, z):
    result = x + y + z
    if result > 100:
        print("Too big!")
    else:
        print("OK")
    return result


class MyClass:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hello, {self.name}"


data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20]
my_dict = {
    "key1": "value1",
    "key2": "value2",
    "key3": "value3",
    "key4": "value4",
}
```

**Configuration (`pyproject.toml`):**

```toml
[tool.black]
line-length = 100
target-version = ['py311']
include = '\.pyi?$'
extend-exclude = '''
/(
  # directories
  \.eggs
  | \.git
  | \.venv
  | build
  | dist
)/
'''
```

**Task 1:** Run Black on the LUMINO MCP server and check the diff!

```bash
cd ~/repos/lumino-mcp-server
black --check --diff src/
```

---

### 1.2 Ruff Format (Black alternative - faster!)

**Why better?** 10-100x faster, written in Rust, Black-compatible.

```bash
# Same as Black, but faster
ruff format ugly_code.py

# Check mode
ruff format --check .

# Diff
ruff format --diff .
```

**Configuration (`pyproject.toml`):**

```toml
[tool.ruff]
line-length = 100
target-version = "py311"

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

---

## 2. Linting - Ruff

**What is linting?** Detecting code errors, bad practices, style issues.

**Ruff advantages:**
- 10-100x faster than flake8/pylint
- Over 700 rules (flake8, pylint, pycodestyle, etc.)
- Written in Rust
- One tool = flake8 + pylint + isort + more

**Installation:**
```bash
uv pip install ruff
```

**Exercise 2: Ruff linting**

Create a `bad_code.py` file:

```python
# bad_code.py - Many issues
import os
import sys
import json
import time  # Unused import

def my_function(x):
    y = x + 1  # Unused variable
    if x == True:  # Bad practice (use "if x:")
        print("yes")
    return x

class myclass:  # Bad class name (should be: MyClass)
    pass

def another_function():
    try:
        result = 10 / 0
    except:  # Too general except
        pass

# Trailing whitespace
name = "test"
```

**Run:**

```bash
# List all issues
ruff check bad_code.py

# Auto-fix (what it can)
ruff check --fix bad_code.py

# Specific rules
ruff check --select F,E bad_code.py  # Only F (pyflakes) and E (pycodestyle)

# Output:
# bad_code.py:4:8: F401 [*] `time` imported but unused
# bad_code.py:7:5: F841 [*] Local variable `y` is assigned to but never used
# bad_code.py:8:8: E712 Comparison to `True` should be `cond is True` or `if cond:`
# bad_code.py:12:7: N801 Class name `myclass` should use CapWords convention
# bad_code.py:17:5: E722 Do not use bare `except`
```

**Configuration (`pyproject.toml`):**

```toml
[tool.ruff]
line-length = 100
target-version = "py311"

# Enable rules
select = [
    "E",   # pycodestyle errors
    "W",   # pycodestyle warnings
    "F",   # pyflakes
    "I",   # isort
    "N",   # pep8-naming
    "UP",  # pyupgrade
    "B",   # flake8-bugbear
    "C4",  # flake8-comprehensions
    "SIM", # flake8-simplify
]

# Disable rules
ignore = [
    "E501",  # line too long (Black handles this)
]

# Skip files
exclude = [
    ".git",
    ".venv",
    "build",
    "dist",
]

[tool.ruff.per-file-ignores]
"__init__.py" = ["F401"]  # Unused imports OK in __init__.py
"tests/*" = ["S101"]      # Assert usage OK in tests
```

**Task 2:** Run Ruff on the Jira MCP server!

```bash
cd ~/repos/jira-mcp-server
ruff check src/
ruff check --fix src/  # Auto-fix
```

---

## 3. Import Sorting - isort

**What it does?** Sorts and groups imports.

**Installation:**
```bash
uv pip install isort
```

**Exercise 3: Sorting import chaos**

Create a `messy_imports.py` file:

```python
# messy_imports.py - Chaotic imports
from myapp.utils import helper
import os
from typing import List, Dict
import sys
from myapp.models import User
import json
from dataclasses import dataclass
import asyncio
from pathlib import Path
```

**Run:**

```bash
# Check
isort --check-only messy_imports.py

# Sort
isort messy_imports.py

# Diff view
isort --diff messy_imports.py
```

**Result:**

```python
# messy_imports.py - After isort
import asyncio
import json
import os
import sys
from dataclasses import dataclass
from pathlib import Path
from typing import Dict, List

from myapp.models import User
from myapp.utils import helper
```

**Configuration (`pyproject.toml`):**

```toml
[tool.isort]
profile = "black"  # Black-compatible
line_length = 100
multi_line_output = 3
include_trailing_comma = true
force_grid_wrap = 0
use_parentheses = true
ensure_newline_before_comments = true

# Import groups
sections = ["FUTURE", "STDLIB", "THIRDPARTY", "FIRSTPARTY", "LOCALFOLDER"]
known_first_party = ["myapp"]
```

**NOTE:** Ruff can also do import sorting! (`ruff check --select I --fix`)

---

## 4. Type Checking - mypy

**What it does?** Checks type hints before runtime.

**Installation:**
```bash
uv pip install mypy
```

**Exercise 4: Type hints and mypy**

Create a `typed_code.py` file:

```python
# typed_code.py
from typing import List, Dict, Optional

def greet(name: str) -> str:
    """Greet a person."""
    return f"Hello, {name}"

def calculate_sum(numbers: List[int]) -> int:
    """Sum a list of integers."""
    return sum(numbers)

def get_user(user_id: int) -> Optional[Dict[str, str]]:
    """Get user by ID."""
    if user_id == 1:
        return {"name": "Alice", "email": "alice@example.com"}
    return None

# Type errors (mypy will catch these)
result = greet(123)  # ERROR: Expected str, got int
total = calculate_sum(["1", "2", "3"])  # ERROR: Expected List[int], got List[str]
user = get_user("invalid")  # ERROR: Expected int, got str
```

**Run:**

```bash
# Type checking
mypy typed_code.py

# Output:
# typed_code.py:17: error: Argument 1 to "greet" has incompatible type "int"; expected "str"
# typed_code.py:18: error: List item 0 has incompatible type "str"; expected "int"
# typed_code.py:19: error: Argument 1 to "get_user" has incompatible type "str"; expected "int"
```

**Fixed version:**

```python
# typed_code.py - Fixed
from typing import List, Dict, Optional

def greet(name: str) -> str:
    return f"Hello, {name}"

def calculate_sum(numbers: List[int]) -> int:
    return sum(numbers)

def get_user(user_id: int) -> Optional[Dict[str, str]]:
    if user_id == 1:
        return {"name": "Alice", "email": "alice@example.com"}
    return None

# Correct usage
result = greet("Alice")  # OK
total = calculate_sum([1, 2, 3])  # OK
user = get_user(1)  # OK
```

**Configuration (`pyproject.toml`):**

```toml
[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_any_unimported = false
no_implicit_optional = true
warn_redundant_casts = true
warn_unused_ignores = true
warn_no_return = true
check_untyped_defs = true
strict_equality = true

# Per-module options
[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false  # Relaxed rules in tests
```

**Task 3:** Add type hints to one of LUMINO server's helper modules and run mypy!

---

## 5. Testing - pytest

**Why pytest?** Simpler, more powerful, better than unittest.

**Installation:**
```bash
uv pip install pytest
```

**Exercise 5: Pytest basics**

Create a `calculator.py` file:

```python
# calculator.py
from typing import Union

def add(a: Union[int, float], b: Union[int, float]) -> Union[int, float]:
    """Add two numbers."""
    return a + b

def subtract(a: Union[int, float], b: Union[int, float]) -> Union[int, float]:
    """Subtract b from a."""
    return a - b

def multiply(a: Union[int, float], b: Union[int, float]) -> Union[int, float]:
    """Multiply two numbers."""
    return a * b

def divide(a: Union[int, float], b: Union[int, float]) -> float:
    """Divide a by b."""
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

Create a `tests/test_calculator.py` file:

```python
# tests/test_calculator.py
import pytest
from calculator import add, subtract, multiply, divide

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

def test_subtract():
    assert subtract(5, 3) == 2
    assert subtract(0, 5) == -5

def test_multiply():
    assert multiply(3, 4) == 12
    assert multiply(-2, 3) == -6

def test_divide():
    assert divide(10, 2) == 5
    assert divide(9, 3) == 3

def test_divide_by_zero():
    """Test exception handling."""
    with pytest.raises(ValueError, match="Cannot divide by zero"):
        divide(10, 0)

# Parametrized tests
@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (0, 0, 0),
    (-1, 1, 0),
    (10, -5, 5),
])
def test_add_parametrized(a, b, expected):
    assert add(a, b) == expected

# Fixtures
@pytest.fixture
def sample_numbers():
    return [1, 2, 3, 4, 5]

def test_with_fixture(sample_numbers):
    assert sum(sample_numbers) == 15
```

**Run:**

```bash
# All tests
pytest

# Verbose mode
pytest -v

# One file
pytest tests/test_calculator.py

# One test
pytest tests/test_calculator.py::test_add

# With coverage
pytest --cov=calculator --cov-report=term-missing

# Parallel execution (faster)
pytest -n auto  # Requires: pip install pytest-xdist
```

**Configuration (`pyproject.toml`):**

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_functions = "test_*"
addopts = [
    "-v",
    "--strict-markers",
    "--tb=short",
]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
]
```

**Task 4:** Write tests for a function in Jira MCP server's utils.py module!

---

## 6. Code Coverage - coverage & pytest-cov

**What it does?** Measures what percentage of code is tested.

**Installation:**
```bash
uv pip install coverage pytest-cov
```

**Exercise 6: Coverage measurement**

```bash
# Collect coverage
pytest --cov=calculator --cov-report=term-missing

# Output:
# Name            Stmts   Miss  Cover   Missing
# ---------------------------------------------
# calculator.py      10      0   100%
# ---------------------------------------------
# TOTAL              10      0   100%

# HTML report (viewable in browser)
pytest --cov=calculator --cov-report=html
open htmlcov/index.html

# XML report (for CI/CD)
pytest --cov=calculator --cov-report=xml
```

**Configuration (`pyproject.toml`):**

```toml
[tool.coverage.run]
source = ["src"]
omit = [
    "*/tests/*",
    "*/__pycache__/*",
    "*/venv/*",
    "*/.venv/*",
]

[tool.coverage.report]
precision = 2
show_missing = true
skip_covered = false
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
    "if __name__ == .__main__.:",
    "if TYPE_CHECKING:",
]

[tool.coverage.html]
directory = "htmlcov"
```

**Goal:** Minimum 80% coverage (ideal: 90%+)

---

## 7. Test Automation - tox

**What it does?** Automates testing across multiple Python versions and environments.

**Installation:**
```bash
uv pip install tox
```

**Exercise 7: Tox configuration**

Create a `tox.ini` file:

```ini
# tox.ini
[tox]
envlist = py311,py312,lint,type
isolated_build = True

[testenv]
deps =
    pytest
    pytest-cov
commands =
    pytest --cov=src --cov-report=term-missing {posargs}

[testenv:lint]
deps =
    ruff
commands =
    ruff check src/ tests/

[testenv:format]
deps =
    black
    ruff
commands =
    black --check src/ tests/
    ruff format --check src/ tests/

[testenv:type]
deps =
    mypy
    pytest
commands =
    mypy src/

[testenv:coverage]
deps =
    pytest
    pytest-cov
commands =
    pytest --cov=src --cov-report=html --cov-report=term-missing

[testenv:all]
deps =
    {[testenv:lint]deps}
    {[testenv:format]deps}
    {[testenv:type]deps}
    {[testenv]deps}
commands =
    {[testenv:format]commands}
    {[testenv:lint]commands}
    {[testenv:type]commands}
    {[testenv]commands}
```

**Run:**

```bash
# All environments
tox

# Specific environment
tox -e lint
tox -e type
tox -e py311

# Parallel execution (faster)
tox -p auto

# Rebuild
tox -r
```

**Practical example - LUMINO MCP server tox.ini:**

```ini
# lumino-mcp-server/tox.ini
[tox]
envlist = py311,lint,type,test
skipsdist = True

[testenv]
deps =
    -r requirements.txt
    pytest
    pytest-asyncio
    pytest-cov

[testenv:test]
commands =
    pytest tests/ --cov=src --cov-report=term-missing -v

[testenv:lint]
deps =
    ruff
commands =
    ruff check src/ tests/

[testenv:format-check]
deps =
    ruff
commands =
    ruff format --check src/ tests/

[testenv:type]
deps =
    mypy
    types-aiofiles
commands =
    mypy src/

[testenv:all]
deps =
    {[testenv:lint]deps}
    {[testenv:format-check]deps}
    {[testenv:type]deps}
    {[testenv]deps}
commands =
    {[testenv:format-check]commands}
    {[testenv:lint]commands}
    {[testenv:type]commands}
    {[testenv:test]commands}
```

**Task 5:** Create tox.ini for the Jira MCP server!

---

## 8. Git Hooks - pre-commit

**What it does?** Automatically runs checks before commit.

**Installation:**
```bash
uv pip install pre-commit
```

**Exercise 8: Pre-commit hooks**

Create a `.pre-commit-config.yaml` file:

```yaml
# .pre-commit-config.yaml
repos:
  # Ruff - linting and formatting
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  # mypy - type checking
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        additional_dependencies: [types-all]

  # Standard hooks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-toml
      - id: check-added-large-files
        args: [--maxkb=1000]
      - id: detect-private-key

  # Security check
  - repo: https://github.com/PyCQA/bandit
    rev: 1.7.6
    hooks:
      - id: bandit
        args: ["-c", "pyproject.toml"]
```

**Setup:**

```bash
# Install in repo
pre-commit install

# Manual run (on all files)
pre-commit run --all-files

# Only one hook
pre-commit run ruff --all-files

# Update hooks
pre-commit autoupdate
```

**Practical usage:**

```bash
# 1. Modify a file
vim src/main.py

# 2. Git add
git add src/main.py

# 3. Commit (pre-commit runs automatically!)
git commit -m "Add new feature"

# If there are errors, commit STOPS and fixes:
# - ruff: auto-fix
# - ruff-format: auto-format
# - mypy: type errors (manual fix needed)
# - trailing-whitespace: auto-fix

# 4. Review and re-commit
git add src/main.py  # Fixed files
git commit -m "Add new feature"
```

**Configuration (`pyproject.toml` - for bandit):**

```toml
[tool.bandit]
exclude_dirs = ["tests", ".venv"]
skips = ["B101"]  # assert_used (OK in tests)
```

**Task 6:** Install pre-commit on the LUMINO MCP server and run it!

---

## 9. Documentation (Optional)

### 9.1 Docstrings

**Google Style (recommended):**

```python
def calculate_discount(price: float, discount_percent: float) -> float:
    """
    Calculate discounted price.

    Args:
        price: Original price in dollars.
        discount_percent: Discount percentage (0-100).

    Returns:
        Discounted price in dollars.

    Raises:
        ValueError: If discount_percent is not between 0 and 100.

    Examples:
        >>> calculate_discount(100, 20)
        80.0
        >>> calculate_discount(50, 10)
        45.0
    """
    if not 0 <= discount_percent <= 100:
        raise ValueError("Discount must be between 0 and 100")
    return price * (1 - discount_percent / 100)
```

**NumPy Style:**

```python
def calculate_discount(price, discount_percent):
    """
    Calculate discounted price.

    Parameters
    ----------
    price : float
        Original price in dollars.
    discount_percent : float
        Discount percentage (0-100).

    Returns
    -------
    float
        Discounted price in dollars.

    Raises
    ------
    ValueError
        If discount_percent is not between 0 and 100.
    """
    pass
```

### 9.2 Sphinx (HTML documentation generation)

```bash
uv pip install sphinx sphinx-rtd-theme

# Initialize
sphinx-quickstart docs

# Build
cd docs
make html
open _build/html/index.html
```

---

## 10. Final Project - Complete Setup

**Goal:** Create a complete Python project with all tools!

### 10.1 Project structure

```
my-awesome-project/
├── src/
│   └── myapp/
│       ├── __init__.py
│       ├── main.py
│       └── utils.py
├── tests/
│   ├── __init__.py
│   └── test_utils.py
├── pyproject.toml
├── tox.ini
├── .pre-commit-config.yaml
├── .gitignore
└── README.md
```

### 10.2 pyproject.toml (Complete config)

```toml
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

[project]
name = "myapp"
version = "0.1.0"
description = "My awesome Python project"
authors = [{name = "Your Name", email = "you@example.com"}]
requires-python = ">=3.11"
dependencies = []

[project.optional-dependencies]
dev = [
    "black",
    "ruff",
    "isort",
    "mypy",
    "pytest",
    "pytest-cov",
    "tox",
    "pre-commit",
]

# Black
[tool.black]
line-length = 100
target-version = ['py311']

# Ruff
[tool.ruff]
line-length = 100
target-version = "py311"
select = ["E", "W", "F", "I", "N", "UP", "B", "C4", "SIM"]
ignore = ["E501"]

[tool.ruff.per-file-ignores]
"__init__.py" = ["F401"]

# isort
[tool.isort]
profile = "black"
line_length = 100

# mypy
[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true

# pytest
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = ["-v", "--cov=src", "--cov-report=term-missing"]

# coverage
[tool.coverage.run]
source = ["src"]
omit = ["*/tests/*"]

[tool.coverage.report]
precision = 2
show_missing = true
exclude_lines = ["pragma: no cover", "if __name__ == .__main__.:"]
```

### 10.3 Makefile (Simple commands)

```makefile
# Makefile
.PHONY: install format lint type test coverage clean all

install:
	uv pip install -e ".[dev]"

format:
	ruff format src/ tests/
	ruff check --fix src/ tests/

lint:
	ruff check src/ tests/

type:
	mypy src/

test:
	pytest

coverage:
	pytest --cov=src --cov-report=html
	open htmlcov/index.html

clean:
	rm -rf .pytest_cache .mypy_cache .ruff_cache htmlcov .coverage
	find . -type d -name "__pycache__" -exec rm -rf {} +

all: format lint type test
```

**Usage:**

```bash
make install    # Install
make format     # Format
make lint       # Lint
make type       # Type check
make test       # Tests
make coverage   # Coverage report
make all        # Everything at once
```

### 10.4 GitHub Actions CI/CD

Create a `.github/workflows/ci.yml` file:

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install uv
        run: pip install uv

      - name: Install dependencies
        run: uv pip install -e ".[dev]"

      - name: Format check
        run: ruff format --check src/ tests/

      - name: Lint
        run: ruff check src/ tests/

      - name: Type check
        run: mypy src/

      - name: Test
        run: pytest --cov=src --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml
```

---

## 📊 Comparison - Tools

| Task | Tool options | Recommendation | Why |
|------|--------------|----------------|-----|
| **Formatting** | Black / Ruff format / yapf | **Ruff format** | Faster, compatible |
| **Linting** | Ruff / flake8 / pylint | **Ruff** | 10-100x faster |
| **Import sort** | isort / Ruff | **Ruff** | One less tool |
| **Type check** | mypy / pyright | **mypy** | Wider support |
| **Testing** | pytest / unittest | **pytest** | Simpler, more powerful |
| **Coverage** | coverage / pytest-cov | **pytest-cov** | Pytest integration |
| **Automation** | tox / nox | **tox** | Wider usage |
| **Git hooks** | pre-commit / husky | **pre-commit** | Python native |

---

##  Recommended Minimal Setup

**For beginners (3 tools):**
1. **Ruff** - format + lint + import sort
2. **pytest** - testing
3. **pre-commit** - git hooks

**For intermediates (+2 tools):**
4. **mypy** - type checking
5. **pytest-cov** - coverage

**For advanced (+1 tool):**
6. **tox** - CI/CD automation

---

##  Practical Tasks

### Task 1: LUMINO MCP Server Setup
```bash
cd ~/repos/lumino-mcp-server

# 1. Create pyproject.toml (tool.ruff, tool.mypy)
# 2. Run Ruff and fix
ruff check --fix src/
ruff format src/

# 3. Add type hints to a module
# 4. Run mypy
mypy src/

# 5. Install pre-commit
pre-commit install
pre-commit run --all-files
```

### Task 2: Jira MCP Server Setup
```bash
cd ~/repos/jira-mcp-server

# 1. Create tox.ini
# 2. Write tests (tests/test_utils.py)
# 3. Run tox
tox -e all

# 4. Coverage target: 80%+
pytest --cov=src --cov-report=term-missing
```

### Task 3: New Project From Scratch
```bash
# 1. Initialize project
mkdir my-project && cd my-project
git init

# 2. pyproject.toml + tox.ini + .pre-commit-config.yaml
# 3. Simple module + tests
# 4. Achieve 100% coverage
# 5. Setup pre-commit
# 6. GitHub Actions CI
```

---

##  Additional Resources

**Official documentation:**
- Ruff: https://docs.astral.sh/ruff/
- Black: https://black.readthedocs.io/
- mypy: https://mypy.readthedocs.io/
- pytest: https://docs.pytest.org/
- tox: https://tox.wiki/
- pre-commit: https://pre-commit.com/

**Useful articles:**
- Ruff vs flake8/pylint: https://docs.astral.sh/ruff/faq/
- pytest best practices: https://docs.pytest.org/en/stable/goodpractices.html
- Type hints guide: https://realpython.com/python-type-checking/

---

##  Next Steps

1.  Install tools: `uv pip install ruff mypy pytest pytest-cov tox pre-commit`
2.  Create pyproject.toml for your existing projects
3.  Add pre-commit
4.  Write tests (goal: 80%+ coverage)
5.  Set up CI/CD (GitHub Actions / GitLab CI)

**Ready?** Start with the **Code Formatting** (Black/Ruff) chapter! 

---

**File location:** `/home/mgreczi/learning-plans/programming/python/Python_Dev_Tools_Learning_Plan.md`
