# Security & Authentication

This document summarizes security patterns I implemented in production systems.

## Authentication
- OAuth2 / OpenID Connect
- Authorization Code Flow with PKCE
- JWT access tokens & refresh tokens
- Secure cookie / token storage

## Authorization
- Role-Based Access Control (RBAC)
- Least-privilege permissions
- Service-to-service authentication (mTLS / short-lived tokens)

## API Security
- HTTPS everywhere
- Rate limiting
- Input validation & sanitization
- SQL injection protection (parameterized queries)
- XSS & CSRF protection
- Audit logging

## Operational Security
- Secrets management (AWS Secrets Manager / env vars)
- Logging & monitoring
- Incident response & alerting
