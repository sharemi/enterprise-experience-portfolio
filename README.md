# Enterprise Experience Portfolio

This repository summarizes my 8+ years of experience building and maintaining
large-scale production systems in enterprise environments (education and financial-scale platforms).

For confidentiality reasons, no proprietary source code or data is shared.
All content here is based on real-world systems I worked on, rewritten as high-level documentation.

## What you’ll find here
- System design and architecture notes
- Security and authentication patterns (OAuth2/OIDC, JWT, RBAC)
- Microservices and event-driven patterns
- Cloud and DevOps practices (AWS, CI/CD)
- Data pipeline and database performance notes
- Frontend integration and accessibility practices (React + WCAG)

## Tech Stack (from real-world experience)
- Backend: Node.js, .NET Core, REST, GraphQL
- Frontend: React, TypeScript
- Cloud: AWS (Lambda, RDS/Aurora PostgreSQL, S3, CloudWatch)
- Messaging: RabbitMQ
- Databases: PostgreSQL, MySQL, MongoDB
- CI/CD: GitLab CI, GitHub Actions

## Note
This portfolio is intended to demonstrate engineering depth and enterprise practices
rather than small demo applications.

## Documentation

- **Architecture Overview**  
  [architecture/README.md](architecture/README.md)
### Single Sign-On (SSO)

- Enterprise OAuth2 / OIDC based authentication service  
  [SSO Architecture & Flow](sso-edlink-style-architecture.md)
- **Authentication & Security**  
  [security/README.md](security/README.md)

- **OAuth2 / OIDC Auth Flow**  
  [diagrams/auth-flow.md](diagrams/auth-flow.md)

- **CI/CD Pipeline Overview**  
  [cicd/pipeline-overview.md](cicd/pipeline-overview.md)
  
## Code Demos
- Secure Auth Service Demo (JWT, RBAC, httpOnly refresh cookie, tests)
  https://github.com/sharemi/secure-auth-service-demo