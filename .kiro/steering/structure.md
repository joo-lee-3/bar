# Project Structure

## Repository Layout

```
ai-platform/
├── services/ai-platform/          # Main Python service
├── charts/                         # Helm charts for deployment
│   ├── ai-platform-core/          # Core service chart
│   └── ai-platform-stratus/       # Stratus deployment chart
├── deploy/                         # ArgoCD deployment config
├── pipelines/                      # CI/CD pipeline definitions
├── sdk/                           # Generated SDKs
│   ├── python/                    # Python SDK
│   └── sdk.sh                     # SDK generation script
├── terraform/                     # Infrastructure as Code
│   ├── modules/                   # Terraform modules
│   └── organizations/             # Organization-specific configs
├── .gitlab-ci.yml                 # Main GitLab CI config
└── .kiro/                         # Kiro IDE configuration
    ├── specs/                     # Design specifications
    └── steering/                  # AI assistant steering rules
```

## Service Structure (`services/ai-platform/`)

```
services/ai-platform/
├── src/ai_platform/               # Source code package
│   ├── api/                       # API endpoint modules
│   ├── dto/                       # Data Transfer Objects
│   ├── providers/                 # LLM provider implementations
│   ├── services/                  # Business logic services
│   ├── app.py                     # FastAPI application entry point
│   ├── dependencies.py            # FastAPI dependency injection
│   ├── exceptions.py              # Custom exception classes
│   └── routing.py                 # Custom route handling (PlatformRoute)
├── test/                          # Unit and integration tests
├── pyproject.toml                 # Python project configuration
├── Dockerfile                     # Container build configuration
└── openapi.json                   # Generated OpenAPI specification
```

## Architecture Patterns

### API Design
- **Clean API Structure**: Direct endpoint organization without versioning
- **Router-based**: Each domain has its own router module
- **DTO Pattern**: Separate request/response models in `dto/` package
- **Exception Handling**: Centralized exception handling with custom exceptions

### Code Organization
- **Layered Architecture**: API → DTO → Business Logic
- **Separation of Concerns**: Clear boundaries between API, data models, and configuration
- **Module Structure**: Each API domain (messages, models) has corresponding DTO module

### Configuration
- **Environment-based**: Settings managed through `config.py`
- **Observability**: Built-in metrics, logging, and tracing
- **Health Checks**: Standard `/heartbeat` and `/healthz` endpoints

## File Naming Conventions
- **API modules**: Descriptive names (messages.py, models.py)
- **DTO modules**: Mirror API module names
- **Test files**: Prefix with `test_` followed by module being tested
- **Configuration files**: Standard names (pyproject.toml, Dockerfile, Chart.yaml)

## Import Patterns
- **Absolute imports**: Use full module paths from package root
- **API routers**: Import and include in main app.py
- **DTOs**: Import specific models as needed
- **Exceptions**: Custom exceptions from exceptions.py module