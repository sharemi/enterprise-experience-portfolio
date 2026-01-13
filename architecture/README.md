# System Architecture (High-Level)

This document describes the architecture patterns I have worked with in large-scale
production systems.

## Core Architecture
- Microservices-based backend
- API Gateway pattern
- Service-to-service communication (REST + async messaging)
- Event-driven workflows using message brokers (RabbitMQ)
- Stateless services with JWT-based authentication
- Centralized logging and monitoring

## Example Components
- Auth Service (OAuth2 / OIDC, JWT, RBAC)
- User / Profile Service
- Assessment / Core Business Service
- Reporting & Analytics Service
- Notification Service
- Data Ingestion & ETL Workers

## Cloud Setup (AWS)
- API Gateway
- Lambda / Containerized Services
- RDS (PostgreSQL / Aurora)
- S3 for file storage
- CloudWatch for logging and monitoring
- CI/CD pipelines for automated testing and deployment

This reflects the real-world systems I worked on in enterprise environments.
