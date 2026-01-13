# Enterprise SSO Service – Architecture & Flow (OAuth2 / OIDC)

This document describes a production-grade Single Sign-On (SSO) microservice
I worked on in an enterprise environment.  
All names and identifiers are generalized for confidentiality.

## Purpose

The service provides centralized authentication by:

- Receiving an OAuth2 Authorization Code from an external Identity Provider (IdP)
- Exchanging the code for Access / Refresh Tokens
- Retrieving user profile and tenant information
- Creating and managing secure user sessions
- Supporting multi-tenant and multi-environment deployments

## High-Level Flow

1. Frontend redirects user to external IdP login page.
2. After successful authentication, IdP redirects back with an **authorization code**.
3. Backend SSO service:
   - Validates the code
   - Calls IdP Token Endpoint
   - Receives `access_token`, `refresh_token`, expiration, scopes
4. Service fetches user profile using Access Token.
5. Tenant and role mapping is resolved.
6. A secure session is created and stored.
7. JWT / session reference is returned to frontend.
8. Subsequent API calls are authorized using RBAC and token validation.

## Architecture

### Runtime
- Node.js (AWS Lambda)
- API Gateway (REST endpoints)
- VPC-connected services

### Data Layer
- DynamoDB: Session storage (per tenant)
- Aurora PostgreSQL: User and organization data

### Security
- OAuth2 / OIDC
- Authorization Code Flow
- Secure token exchange
- Secrets stored in AWS Secrets Manager / Parameter Store
- Short-lived access tokens
- Refresh token rotation
- RBAC and tenant isolation

### Infrastructure

- AWS CDK (TypeScript)
- Multi-environment setup:
  - Sandbox
  - QA
  - Staging
  - Production
- Separate AWS accounts and IdP applications per environment
- Environment-specific configuration via:
  - SSM Parameters
  - Secrets Manager
  - IAM Roles

## Internal Components

### Auth Handler
- Validates incoming authorization codes
- Orchestrates token exchange and profile retrieval

### Token Exchange Service
- Communicates with IdP Token Endpoint
- Handles refresh token lifecycle
- Manages expiration and rotation

### Profile Service
- Retrieves identity and tenant attributes
- Maps roles and permissions

### Session Management
- DynamoDB-backed session store
- Supports logout, refresh, and session invalidation

## Enterprise Characteristics

- Stateless microservice
- Horizontal scalability
- Secure service-to-service communication
- Environment isolation
- Observability via structured logging and metrics

This SSO service follows modern enterprise security standards and is aligned with
banking-grade authentication and authorization architectures.
