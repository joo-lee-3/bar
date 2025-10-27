# Technology Stack

## Core Technologies

- **Python 3.10**: Primary language (strict version constraint <3.11)
- **FastAPI**: Web framework for REST APIs
- **Pydantic v1**: Data validation and settings management
- **Gunicorn + Uvicorn**: ASGI server setup for production

## Development Tools

- **uv**: Modern Python package manager (replaces pip/poetry)
- **Ruff**: Fast Python linter and formatter
- **pytest**: Testing framework with async support
- **poethepoet (poe)**: Task runner for development commands

## Infrastructure & Deployment

- **Docker**: Multi-stage builds with Amazon Linux 2023 base
- **Helm**: Kubernetes deployment with custom charts
- **ArgoCD**: GitOps deployment automation
- **GitLab CI/CD**: Pipeline automation

## Observability

- **OpenTelemetry**: Distributed tracing
- **Prometheus**: Metrics collection (port 8001)
- **Custom logging**: JSON structured logging

## Common Commands

### Development
```bash
# Setup environment
cd services/ai-platform
uv sync

# Code quality
uv run poe lint          # Fix linting issues
uv run poe lint_check    # Check only
uv run poe fmt           # Format code
uv run poe fmt_check     # Check formatting

# Testing
uv run poe test_coverage      # Unit tests with coverage
uv run poe test_integration   # Integration tests

# OpenAPI spec
uv run poe spec              # Generate openapi.json
uv run poe spec custom.json  # Generate with custom filename
```

### Docker
```bash
# Build (requires BASE_IMAGE arg)
docker build --build-arg BASE_IMAGE=<base-image> -t ai-platform .

# Run locally
docker run -p 8000:8000 ai-platform
```

## Code Standards

- **Line length**: 120 characters (Ruff configured)
- **Import style**: Absolute imports preferred
- **Type hints**: Required for public APIs
- **Async/await**: Use for I/O operations
- **Error handling**: Custom PlatformServiceException for business errors
- **Comments**: Use reST docstrings for complex public methods; Avoid boilerplate docstrings 
