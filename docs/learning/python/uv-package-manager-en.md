# UV - Modern Python Package Manager - Learning Plan

## Overview

This learning plan covers **uv**, a modern Python package and project manager built in Rust. UV is an extremely fast alternative to traditional Python tools (pip, pip-tools, poetry, pipenv).

**Target Audience:** Python developers, DevOps engineers, Infrastructure team members

**Time Required:** 2-3 weeks (3-5 hours/week)

**Prerequisites:**
- Basic Python knowledge
- Virtual environments (venv) experience
- pip usage experience
- Terminal/CLI familiarity

---

## What is uv?

**uv** is Astral's modern Python package and project manager:

- **Written in Rust** → 10-100x faster than pip
- **Unified tool** → Replaces pip, pip-tools, pipenv, poetry, pyenv
- **Drop-in replacement** → Compatible with pip commands
- **Lock files** → Deterministic builds (uv.lock)
- **pyproject.toml** → Modern Python project standard
- **Workspace support** → Monorepo project management
- **Python version management** → Built-in Python version control

**Why important for Infrastructure team?**
- MCP servers (lumino, jira, servicenow) all use uv
- Fast dependency installation in CI/CD pipelines
- Reproducible builds (uv.lock)
- Simplified developer experience

---

## Module 1: Installation and First Steps

### Learning Objectives
- Install uv on different platforms
- Learn basic commands
- Create first project

### Content

#### 1.1 Installation

**Linux/macOS (standalone installer):**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Fedora/RHEL (from official repo):**
```bash
# Expected in official repos in 2026
dnf install uv
```

**Homebrew (macOS):**
```bash
brew install uv
```

**Cargo (Rust package manager):**
```bash
cargo install uv
```

**Version check:**
```bash
uv --version
# uv 0.5.24 (or newer)
```

#### 1.2 uv vs pip - Comparison

| Task | pip | uv |
|------|-----|-----|
| Package install | `pip install requests` | `uv pip install requests` |
| Requirements file | `pip install -r requirements.txt` | `uv pip install -r requirements.txt` |
| Project init | Manual setup.py | `uv init` |
| Lock file | pip-compile | Automatic (uv.lock) |
| Speed | Baseline | 10-100x faster |

#### 1.3 First Project - Hello UV

```bash
# Create new project
mkdir hello-uv
cd hello-uv
uv init

# Project structure
# hello-uv/
# pyproject.toml
# README.md
# hello.py

# Run Python script (automatic venv management)
uv run hello.py

# Add dependency
uv add requests

# Run script with dependency
uv run python -c "import requests; print(requests.__version__)"
```

### Exercise 1.1: First uv Project

**Task:** Create a simple Python script that queries the GitHub API.

```bash
# 1. Create project
mkdir github-fetcher
cd github-fetcher
uv init

# 2. Add dependencies
uv add requests

# 3. Write script (main.py)
cat > main.py << 'EOF'
import requests

def get_user(username):
 response = requests.get(f"https://api.github.com/users/{username}")
 return response.json()

if __name__ == "__main__":
 user = get_user("astral-sh")
 print(f"Name: {user['name']}")
 print(f"Public repos: {user['public_repos']}")
EOF

# 4. Run
uv run python main.py
```

**Verification:**
- `pyproject.toml` contains `requests` dependency
- `uv.lock` was created
- Script ran successfully

---

## Module 2: Virtual Environments and Dependency Management

### Learning Objectives
- Understand uv venv management
- Install and update dependencies
- Use lock files

### Content

#### 2.1 Virtual Environments

**Automatic venv (recommended):**
```bash
# uv run automatically creates .venv
uv run python script.py

# Activate manually (if needed)
source .venv/bin/activate # Linux/macOS
.venv\Scripts\activate # Windows
```

**Manual venv creation:**
```bash
# Create new venv
uv venv

# With specific Python version
uv venv --python 3.12

# Custom name
uv venv my-custom-env
```

#### 2.2 Dependency Management

**Installing packages:**
```bash
# Single package
uv add requests

# With version specification
uv add "fastapi>=0.100.0"

# Dev dependency
uv add --dev pytest pytest-cov

# Optional dependency group
uv add --group docs sphinx
```

**Removing packages:**
```bash
uv remove requests
```

**Full sync (based on lock file):**
```bash
uv sync
```

#### 2.3 Lock Files (uv.lock)

The `uv.lock` file ensures **deterministic builds**:

- Contains exact versions of all dependencies
- Includes hashes for security
- Automatically updates with `uv add`, `uv remove`
- Committed to Git (unlike `requirements.txt`)

**Updating lock file:**
```bash
# Update all dependencies
uv lock --upgrade

# Update single package
uv lock --upgrade-package requests
```

### Exercise 2.1: MCP Client Project

**Task:** Create a simple MCP (Model Context Protocol) client project.

```bash
# 1. Project init
mkdir mcp-test-client
cd mcp-test-client
uv init

# 2. Add MCP SDK
uv add mcp

# 3. Add dev tools
uv add --dev pytest ruff

# 4. Simple MCP client (client.py)
cat > client.py << 'EOF'
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def main():
 server_params = StdioServerParameters(
 command="uvx",
 args=["mcp-server-time"]
 )

 async with stdio_client(server_params) as (read, write):
 async with ClientSession(read, write) as session:
 await session.initialize()
 tools = await session.list_tools()
 print(f"Available tools: {[t.name for t in tools.tools]}")

if __name__ == "__main__":
 import asyncio
 asyncio.run(main())
EOF

# 5. Run
uv run python client.py
```

**Learnings:**
- `uv add` automatically updates `pyproject.toml` and `uv.lock`
- Dev dependencies separation (`--dev`)
- `uv run` automatically installs missing packages

---

## Module 3: pyproject.toml and Project Configuration

### Learning Objectives
- Understand pyproject.toml structure
- Configure project metadata
- Setup build system

### Content

#### 3.1 pyproject.toml Anatomy

```toml
[project]
name = "my-awesome-project"
version = "0.1.0"
description = "A brief description"
authors = [
 { name = "Documentation Team", email = "developer@company.com" }
]
readme = "README.md"
requires-python = ">=3.10"
dependencies = [
 "requests>=2.31.0",
 "pydantic>=2.0.0",
]

[project.optional-dependencies]
dev = [
 "pytest>=7.0.0",
 "ruff>=0.1.0",
]
docs = [
 "sphinx>=7.0.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.uv]
dev-dependencies = [
 "pytest>=8.0.0",
]

[tool.ruff]
line-length = 100
select = ["E", "F", "I"]

[tool.pytest.ini_options]
testpaths = ["tests"]
```

#### 3.2 Dependency Groups

```toml
[project.optional-dependencies]
# Documentation generation
docs = ["sphinx", "sphinx-rtd-theme"]

# ML/Data science stack
ml = ["numpy", "pandas", "scikit-learn"]

# API development
api = ["fastapi", "uvicorn", "pydantic"]
```

**Installation:**
```bash
# Install one group
uv sync --group docs

# Multiple groups
uv sync --group docs --group ml

# All groups
uv sync --all-groups
```

#### 3.3 Scripts and Entry Points

```toml
[project.scripts]
my-cli = "my_package.cli:main"
serve = "my_package.server:run"

[project.entry-points."mcp.servers"]
my-mcp-server = "my_package.server:main"
```

**Usage:**
```bash
# Automatically available in PATH after uv sync
uv run my-cli --help
uv run serve --port 8080
```

### Exercise 3.1: Platform Lumino MCP Server Analysis

**Task:** Analyze the lumino-mcp-server `pyproject.toml` file.

```bash
cd ~/ai/platform-tools/mcp-servers/lumino-mcp-server
cat pyproject.toml
```

**Questions:**
1. What Python version is required? (`requires-python`)
2. What are the main dependencies? (`dependencies`)
3. Is there a dev dependency group? (`tool.uv.dev-dependencies`)
4. What is the build backend? (`build-system.build-backend`)
5. What entry points are defined?

**Solution:**
```bash
# 1. Check Python version
grep "requires-python" pyproject.toml

# 2. List dependencies
uv tree

# 3. Check dev dependencies
grep -A 10 "dev-dependencies" pyproject.toml

# 4. Build backend
grep "build-backend" pyproject.toml
```

---

## Module 4: uv pip - Drop-in pip Replacement

### Learning Objectives
- Use uv pip commands
- Understand pip compatibility
- Migrate from pip to uv

### Content

#### 4.1 uv pip Commands

| pip command | uv equivalent | Note |
|-------------|---------------|------|
| `pip install requests` | `uv pip install requests` | Exact compatibility |
| `pip install -r requirements.txt` | `uv pip install -r requirements.txt` | 10-100x faster |
| `pip freeze` | `uv pip freeze` | List installed packages |
| `pip list` | `uv pip list` | Packages in table format |
| `pip show requests` | `uv pip show requests` | Package details |
| `pip uninstall requests` | `uv pip uninstall requests` | Remove package |

#### 4.2 Requirements Files

**Generate requirements.txt:**
```bash
# Export installed packages
uv pip freeze > requirements.txt

# Install from requirements.txt
uv pip install -r requirements.txt
```

**requirements.txt vs pyproject.toml:**

| Feature | requirements.txt | pyproject.toml |
|---------|------------------|----------------|
| Standard | Old (PEP 440) | Modern (PEP 621) |
| Metadata | None | Full project info |
| Lock support | External tool (pip-compile) | Built-in (uv.lock) |
| Scripts | None | Entry points |
| Dev deps | Separate file (dev-requirements.txt) | Separated groups |

#### 4.3 Migration from pip to uv

**Step 1: requirements.txt → pyproject.toml conversion**

```bash
# Old project with requirements.txt
cat requirements.txt
# requests==2.31.0
# pydantic==2.5.0
# fastapi==0.104.1

# uv init (creates pyproject.toml)
uv init --name my-legacy-project

# Add dependencies
uv add requests pydantic fastapi

# Verify
cat pyproject.toml
```

**Step 2: Update CI/CD pipeline**

```yaml
# Old (.gitlab-ci.yml or .github/workflows/test.yml)
- pip install -r requirements.txt
- pip install pytest
- pytest

# New (with uv)
- curl -LsSf https://astral.sh/uv/install.sh | sh
- uv sync
- uv run pytest
```

### Exercise 4.1: Legacy Project Migration

**Task:** Create an old pip-based project and migrate to uv.

```bash
# 1. Simulate old project
mkdir legacy-app
cd legacy-app

cat > requirements.txt << EOF
flask==3.0.0
sqlalchemy==2.0.23
pytest==7.4.3
EOF

cat > app.py << EOF
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
 return "Hello from Flask!"

if __name__ == '__main__':
 app.run(debug=True)
EOF

# 2. Migrate to uv
uv init --name legacy-app

# 3. Add dependencies
uv add flask sqlalchemy
uv add --dev pytest

# 4. Delete requirements.txt (no longer needed)
rm requirements.txt

# 5. Run
uv run python app.py
```

**Verification:**
- `pyproject.toml` created
- `uv.lock` created
- Flask app starts with `uv run`
- No more manual `pip install` needed

---

## Module 5: Workspace and Monorepo Management

### Learning Objectives
- Manage multi-package projects
- Configure workspace
- Handle shared dependencies

### Content

#### 5.1 Workspace Concept

**Workspace example:**
```
platform-mcp-workspace/
 pyproject.toml # Root workspace config
 uv.lock # Shared lock file
 packages/
 jira-mcp-server/
 pyproject.toml
 src/
 lumino-mcp-server/
 pyproject.toml
 src/
 servicenow-mcp-server/
 pyproject.toml
 src/
```

**Root pyproject.toml:**
```toml
[tool.uv.workspace]
members = [
 "packages/jira-mcp-server",
 "packages/lumino-mcp-server",
 "packages/servicenow-mcp-server",
]

[tool.uv]
dev-dependencies = [
 "pytest>=8.0.0",
 "ruff>=0.3.0",
]
```

#### 5.2 Workspace Commands

```bash
# Full workspace sync
uv sync

# Build single package
cd packages/jira-mcp-server
uv build

# Run tests across workspace
uv run pytest

# Add dependency to specific package
cd packages/lumino-mcp-server
uv add prometheus-client
```

#### 5.3 Shared Dependencies

**Benefits:**
- Single `uv.lock` for entire workspace
- Shared dev tools (pytest, ruff)
- Faster CI/CD (downloads everything once)
- Version conflict detection

### Exercise 5.1: Mini MCP Workspace

**Task:** Create a workspace with two MCP servers.

```bash
# 1. Create root workspace
mkdir mcp-workspace
cd mcp-workspace
uv init --name mcp-workspace

# 2. Workspace configuration
cat > pyproject.toml << EOF
[tool.uv.workspace]
members = ["servers/*"]

[tool.uv]
dev-dependencies = [
 "pytest>=8.0.0",
]
EOF

# 3. First MCP server
mkdir -p servers/time-server
cd servers/time-server
uv init --name time-server
uv add mcp

cat > src/server.py << 'EOF'
from mcp.server import Server
import datetime

server = Server("time-server")

@server.list_tools()
async def list_tools():
 return [{
 "name": "get_time",
 "description": "Get current time"
 }]
EOF

# 4. Second MCP server
cd ../..
mkdir -p servers/weather-server
cd servers/weather-server
uv init --name weather-server
uv add mcp requests

# 5. Workspace sync
cd ../..
uv sync

# 6. Verify
uv tree
```

**Learnings:**
- One `uv.lock` manages both servers
- Shared dev dependencies (pytest)
- Common dependency resolution

---

## Module 6: Python Version Management

### Learning Objectives
- Manage Python versions with uv
- Use project-specific Python versions
- Replace pyenv

### Content

#### 6.1 Python Installation with uv

```bash
# List available Python versions
uv python list

# Install specific version
uv python install 3.12

# Install multiple versions
uv python install 3.11 3.12 3.13

# List installed versions
uv python list --only-installed
```

#### 6.2 Project Python Version

```bash
# Create project with specific Python version
uv init --python 3.12

# Check pyproject.toml
cat pyproject.toml
# requires-python = ">=3.12"

# Virtual environment with specific version
uv venv --python 3.11
```

#### 6.3 Python Version Pinning

**.python-version file:**
```bash
# Project-level version pinning
echo "3.12" > .python-version

# uv automatically uses it
uv run python --version
# Python 3.12.x
```

### Exercise 6.1: Multi-version Testing

**Task:** Test a library under Python 3.11 and 3.12.

```bash
# 1. Project setup
mkdir multi-python-test
cd multi-python-test
uv init

# 2. Test script
cat > test_compatibility.py << EOF
import sys

def test_version():
 print(f"Running on Python {sys.version}")
 assert sys.version_info >= (3, 11)

if __name__ == "__main__":
 test_version()
EOF

# 3. Python 3.11 test
uv venv --python 3.11 .venv-py311
.venv-py311/bin/python test_compatibility.py

# 4. Python 3.12 test
uv venv --python 3.12 .venv-py312
.venv-py312/bin/python test_compatibility.py

# 5. Cleanup
rm -rf .venv-py311 .venv-py312
```

---

## Module 7: CI/CD Integration

### Learning Objectives
- Use uv in GitLab CI/CD
- GitHub Actions integration
- Docker build optimization

### Content

#### 7.1 GitLab CI/CD

**.gitlab-ci.yml example:**
```yaml
image: python:3.12-slim

stages:
 - test
 - build

variables:
 UV_CACHE_DIR: .uv-cache

# Install uv with caching
.install-uv: &install-uv
 - curl -LsSf https://astral.sh/uv/install.sh | sh
 - export PATH="$HOME/.cargo/bin:$PATH"

cache:
 key: ${CI_COMMIT_REF_SLUG}
 paths:
 - .uv-cache/

test:
 stage: test
 before_script:
 - *install-uv
 script:
 - uv sync
 - uv run pytest tests/
 - uv run ruff check .
 coverage: '/(?i)total.*? (100(?:\.0+)?\%|[1-9]?\d(?:\.\d+)?\%)$/'

build:
 stage: build
 before_script:
 - *install-uv
 script:
 - uv build
 artifacts:
 paths:
 - dist/
```

#### 7.2 GitHub Actions

**.github/workflows/test.yml:**
```yaml
name: Test

on: [push, pull_request]

jobs:
 test:
 runs-on: ubuntu-latest
 strategy:
 matrix:
 python-version: ["3.11", "3.12"]

 steps:
 - uses: actions/checkout@v4

 - name: Install uv
 uses: astral-sh/setup-uv@v1
 with:
 version: "latest"

 - name: Set up Python ${{ matrix.python-version }}
 run: uv python install ${{ matrix.python-version }}

 - name: Install dependencies
 run: uv sync --all-extras --dev

 - name: Run tests
 run: uv run pytest

 - name: Lint
 run: uv run ruff check .
```

#### 7.3 Docker Multi-Stage Build

**Dockerfile example:**
```dockerfile
# Build stage
FROM python:3.12-slim AS builder

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/uv

# Copy project files
WORKDIR /app
COPY pyproject.toml uv.lock ./
COPY src/ ./src/

# Install dependencies
RUN uv sync --frozen --no-dev

# Runtime stage
FROM python:3.12-slim

# Copy venv from builder
COPY --from=builder /app/.venv /app/.venv
COPY --from=builder /app/src /app/src

# Set PATH
ENV PATH="/app/.venv/bin:$PATH"

# Run application
CMD ["python", "-m", "src.main"]
```

**Benefits:**
- Fast build (uv cache)
- Small image size (multi-stage)
- Reproducible (uv.lock)

### Exercise 7.1: GitLab CI Setup

**Task:** Create a complete CI/CD pipeline with uv.

```bash
# 1. Create project
mkdir ci-demo
cd ci-demo
uv init
uv add fastapi
uv add --dev pytest pytest-cov ruff

# 2. Simple API
cat > src/api.py << EOF
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
 return {"message": "Hello CI/CD"}

@app.get("/health")
def health():
 return {"status": "healthy"}
EOF

# 3. Tests
mkdir tests
cat > tests/test_api.py << EOF
from fastapi.testclient import TestClient
from src.api import app

client = TestClient(app)

def test_root():
 response = client.get("/")
 assert response.status_code == 200
 assert response.json() == {"message": "Hello CI/CD"}

def test_health():
 response = client.get("/health")
 assert response.status_code == 200
EOF

# 4. GitLab CI configuration
cat > .gitlab-ci.yml << EOF
image: python:3.12-slim

stages:
 - test

test:
 stage: test
 before_script:
 - curl -LsSf https://astral.sh/uv/install.sh | sh
 - export PATH="\$HOME/.cargo/bin:\$PATH"
 script:
 - uv sync
 - uv run pytest -v
 - uv run ruff check .
EOF

# 5. Local test
uv run pytest -v
uv run ruff check .
```

---

## Module 8: Advanced Topics

### Learning Objectives
- Custom package indexes
- Private PyPI servers
- Performance tuning

### Content

#### 8.1 Private Package Index

**Internal PyPI configuration:**

```toml
# pyproject.toml
[[tool.uv.index]]
name = "company-internal"
url = "https://pypi.company.com/simple"
default = false

[[tool.uv.index]]
name = "pypi"
url = "https://pypi.org/simple"
default = true
```

**Environment variables:**
```bash
export UV_INDEX_URL="https://pypi.company.com/simple"
export UV_EXTRA_INDEX_URL="https://pypi.org/simple"
```

#### 8.2 Custom Sources

```toml
# Git repository dependency
[project]
dependencies = [
 "my-package @ git+https://github.com/org/repo.git@v1.0.0",
]

# Local path dependency
dependencies = [
 "my-local-package @ file:///home/user/projects/my-package",
]
```

#### 8.3 Performance Tuning

```bash
# Parallel downloads (default: auto)
export UV_CONCURRENT_DOWNLOADS=10

# Cache directory
export UV_CACHE_DIR=/tmp/uv-cache

# Offline mode (cache only)
uv sync --offline

# Reinstall all packages
uv sync --reinstall

# Prune unused cache
uv cache clean
```

#### 8.4 Resolution Strategies

```toml
[tool.uv]
# Lowest compatible versions (stability)
resolution = "lowest-direct"

# Highest compatible versions (default)
resolution = "highest"
```

### Exercise 8.1: Private Package Index

**Task:** Simulate using a private PyPI.

```bash
# 1. Local PyPI server (pypiserver)
mkdir -p ~/pypi-packages
uv tool install pypiserver

# 2. Simple package build
mkdir my-internal-lib
cd my-internal-lib
uv init --name internal-utils

cat > src/internal_utils/__init__.py << EOF
def secret_function():
 return "This is from internal PyPI"
EOF

# 3. Build
uv build

# 4. Upload to local PyPI
cp dist/*.whl ~/pypi-packages/

# 5. Start PyPI server
uv tool run pypiserver run -p 8080 ~/pypi-packages &

# 6. Client project
cd ..
mkdir pypi-client
cd pypi-client
uv init

# 7. Configure with private index
cat >> pyproject.toml << EOF
[[tool.uv.index]]
name = "local-pypi"
url = "http://localhost:8080/simple"
default = true
EOF

# 8. Install from private PyPI
uv add internal-utils

# 9. Cleanup
pkill -f pypiserver
```

---

## Module 9: Debugging and Troubleshooting

### Learning Objectives
- Resolve dependency conflicts
- Debug lock files
- Use verbose logging

### Content

#### 9.1 Dependency Conflicts

**Conflict detection:**
```bash
# Verbose mode
uv add --verbose some-package

# Dependency tree
uv tree

# Specific package dependencies
uv tree --package requests
```

**Example conflict:**
```bash
# Package A requires pandas<2.0
# Package B requires pandas>=2.0

uv add package-a package-b
# Error: No solution found when resolving dependencies
```

**Solution:**
```bash
# 1. Check
uv tree --package package-a
uv tree --package package-b

# 2. Relax version constraint
uv add "package-a>=1.0,<2.0"

# 3. Or find alternative
uv search similar-to-package-a
```

#### 9.2 Lock File Issues

**Regenerate lock file:**
```bash
# Delete and regenerate full lock file
rm uv.lock
uv lock

# Upgrade lockfile
uv lock --upgrade

# Dry-run (what would change?)
uv lock --dry-run
```

**Analyze lock file:**
```bash
# Lock file debug info
uv lock --verbose

# Why specific package is locked
grep -A 5 "name = \"requests\"" uv.lock
```

#### 9.3 Cache Issues

```bash
# Clean cache
uv cache clean

# Cache info
uv cache dir
uv cache prune

# Reinstall without cache
uv sync --reinstall --no-cache
```

### Exercise 9.1: Conflict Resolution

**Task:** Simulate and resolve a dependency conflict.

```bash
# 1. Create project
mkdir conflict-demo
cd conflict-demo
uv init

# 2. Induce conflict
# Install packages with overlapping dependencies

uv add requests # Depends: urllib3<3,>=1.21.1
uv add botocore # Depends: urllib3<2.0,>=1.25.4

# 3. Check
uv tree

# 4. Solution - explicit version
# If conflict exists, try:
uv lock --verbose 2>&1 | grep -i conflict

# 5. Version pinning
cat >> pyproject.toml << EOF
# [tool.uv]
# override-dependencies = ["urllib3==1.26.18"]
EOF

uv lock
```

---

## Module 10: Platform-Specific Use Cases

### Learning Objectives
- Manage MCP server projects
- OpenShift deployment
- Internal tooling

### Content

#### 10.1 Lumino MCP Server Development

**Developer workflow:**
```bash
# 1. Clone and setup
cd ~/ai/platform-tools/mcp-servers/lumino-mcp-server
uv sync

# 2. Develop new tool
uv run python -c "from src.server_mcp import list_namespaces; print(list_namespaces.__doc__)"

# 3. Tests
uv run pytest tests/

# 4. Linting
uv run ruff check src/
uv run ruff format src/

# 5. Type checking (if mypy available)
uv add --dev mypy
uv run mypy src/
```

#### 10.2 Jira MCP Server

**Bearer token auth setup:**
```bash
cd ~/repos/jira-mcp-server

# Environment variables
export JIRA_URL="https://jira.company.com"
export JIRA_EMAIL="developer@company.com"
export JIRA_TOKEN="$JIRA_API_TOKEN"
export PYTHONUNBUFFERED=1

# Run
uv run python main.py

# MCP client config (Claude Desktop)
cat ~/.config/Claude/claude_desktop_config.json
```

#### 10.3 Container Build Pipeline

**Optimized Dockerfile with uv:**
```dockerfile
# Lumino MCP Server Dockerfile example
FROM registry.company.com/ubi9/python-312:latest AS builder

USER root

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/uv

WORKDIR /app

# Copy dependency files
COPY pyproject.toml uv.lock ./

# Install dependencies (no dev)
RUN uv sync --frozen --no-dev

# Copy source
COPY src/ ./src/

# Runtime stage
FROM registry.company.com/ubi9/python-312:latest

WORKDIR /app

# Copy venv from builder
COPY --from=builder /app/.venv /app/.venv
COPY --from=builder /app/src /app/src

ENV PATH="/app/.venv/bin:$PATH"
ENV PYTHONUNBUFFERED=1

EXPOSE 8000

CMD ["python", "main.py"]
```

**Build and push:**
```bash
# Build
podman build -t quay.io/geored/lumino-mcp-server:latest .

# Push (internal registry)
podman push quay.io/geored/lumino-mcp-server:latest
```

#### 10.4 OpenShift Deployment

**ConfigMap with pyproject.toml:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
 name: lumino-config
data:
 pyproject.toml: |
 [project]
 name = "lumino-mcp-server"
 version = "1.0.0"
 dependencies = [
 "mcp>=1.0.0",
 "kubernetes>=28.0.0",
 ]
```

**Deployment:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: lumino-mcp-server
spec:
 replicas: 1
 template:
 spec:
 containers:
 - name: server
 image: quay.io/geored/lumino-mcp-server:latest
 env:
 - name: KUBERNETES_NAMESPACE
 valueFrom:
 fieldRef:
 fieldPath: metadata.namespace
 - name: PYTHONUNBUFFERED
 value: "1"
```

### Exercise 10.1: ServiceNow MCP Server Setup

**Task:** Create the servicenow-mcp-server project with uv.

```bash
# 1. Project init (if not exists)
mkdir -p ~/ai/platform-tools/mcp-servers/servicenow-mcp-server
cd ~/ai/platform-tools/mcp-servers/servicenow-mcp-server

# 2. uv init
uv init --name servicenow-mcp-server

# 3. Dependencies
uv add mcp aiohttp pydantic
uv add --dev pytest pytest-asyncio ruff mypy

# 4. Project structure
mkdir -p src/helpers tests

# 5. Main server file
cat > src/server_mcp.py << 'EOF'
from mcp.server import Server
import logging

logger = logging.getLogger(__name__)
server = Server("servicenow-mcp-server")

@server.list_tools()
async def list_tools():
 return [
 {
 "name": "get_incident",
 "description": "Get ServiceNow incident details",
 "inputSchema": {
 "type": "object",
 "properties": {
 "incident_id": {"type": "string"}
 },
 "required": ["incident_id"]
 }
 }
 ]

@server.call_tool()
async def call_tool(name: str, arguments: dict):
 if name == "get_incident":
 incident_id = arguments["incident_id"]
 # TODO: Implement ServiceNow API call
 return {
 "content": [{
 "type": "text",
 "text": f"Incident {incident_id} details"
 }]
 }
EOF

# 6. Entry point
cat > main.py << 'EOF'
import asyncio
from mcp.server.stdio import stdio_server
from src.server_mcp import server

async def main():
 async with stdio_server() as (read_stream, write_stream):
 await server.run(
 read_stream,
 write_stream,
 server.create_initialization_options()
 )

if __name__ == "__main__":
 asyncio.run(main())
EOF

# 7. Test
uv run python main.py --help

# 8. Git commit preparation (User will do it!)
cat > GIT_COMMANDS.txt << EOF
# ServiceNow MCP Server - Git Setup
cd ~/ai/platform-tools/mcp-servers/servicenow-mcp-server

git init
git add .
git commit -m "feat: Initial ServiceNow MCP Server setup with uv"

# Push to remote (after creating repo)
# git remote add origin https://github.com/username/servicenow-mcp-server.git
# git push -u origin main
EOF
```

---

## Summary and Next Steps

### What You Learned

1. **uv basics** - Installation, project init, `uv run`
2. **Dependency management** - `uv add`, lock files, sync
3. **pyproject.toml** - Modern Python project configuration
4. **uv pip** - pip replacement, migration
5. **Workspace** - Monorepo management
6. **Python versions** - Version control, multi-version testing
7. **CI/CD** - GitLab, GitHub Actions, Docker
8. **Advanced** - Private PyPI, performance tuning
9. **Debugging** - Conflict resolution, troubleshooting
10. **platform use cases** - MCP servers, OpenShift deployment

### Recommended Practice

**Week 1:**
- Install uv on your machine
- Create 3-5 small projects (`uv init`)
- Try `uv pip` commands

**Week 2:**
- Migrate an existing pip-based project
- Setup CI/CD pipeline (GitLab or GitHub)
- Write Dockerfile with uv

**Week 3:**
- Create workspace project (2-3 packages)
- Develop MCP server tool in Lumino
- Practice performance tuning and debugging

### Useful Links

- **Official Documentation:** https://docs.astral.sh/uv/
- **GitHub:** https://github.com/astral-sh/uv
- **Discord:** https://discord.gg/astral-sh
- **Blog:** https://astral.sh/blog

### Internal Resources

- Lumino MCP Server: `~/ai/platform-tools/mcp-servers/lumino-mcp-server/`
- Jira MCP Server: `~/repos/jira-mcp-server/`
- ServiceNow MCP: `~/ai/platform-tools/mcp-servers/servicenow-mcp-server/`
- Claude Code MEMORY: `~/.claude/projects/-home-demo_user-ai-konflux-lumino/memory/`

### Next Learning Plans

If you enjoyed this learning plan, consider these next:
- **Ruff** - Python linter and formatter (uv compatible)
- **MCP Protocol** - Model Context Protocol in depth
- **FastAPI** - Modern async Python API framework
- **Kubernetes Operators** - Python-based K8s automation

---

**Version:** 1.0.0
**Last Updated:** 2026-03-25
**Author:** Claude Code + Documentation Team
**Project:** Platform Infrastructure - Learning Plans
