# AI Platform Product Overview

The AI Platform is Appian's shared generative AI platform service. It provides a centralized foundation for AI capabilities across Appian's ecosystem.

## Key Characteristics

- **FastAPI-based service**: RESTful API service built with modern Python web framework
- **Cloud-native**: Designed for Kubernetes deployment with proper observability
- **Enterprise-ready**: Includes security, monitoring, and compliance features
- **Microservice architecture**: Part of Appian's distributed service ecosystem

## Current Status

This is an early-stage platform (v0.1.0) with basic scaffolding and hello-world functionality. The service is set up with production-ready infrastructure patterns but minimal business logic.

## Target Environment

- Deployed on Appian Stratus (internal Kubernetes platform)
- Uses GitLab CI/CD for automation
- Integrates with Appian's observability and security stack