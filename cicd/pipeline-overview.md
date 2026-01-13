# CI/CD Pipeline Overview (GitLab CI)

This document explains the CI/CD workflow implemented in GitLab CI for a Node.js frontend application.
It follows an enterprise-style pipeline with automated tests, security scanning, environment-specific builds,
and AWS deployments using OIDC (web identity) with short-lived credentials.

- Environment URLs, bucket names, and role ARNs are intentionally generalized for confidentiality.
- This is a high-level explanation of a real production pipeline pattern.

---

## Pipeline Stages

The pipeline is organized into four stages:

1. **test**
2. **snyk_code_scan**
3. **build**
4. **deploy**

---

## 1 Test Stage

### `test`
**Purpose:** Run automated test suite on merge requests and key branches.

**Runtime:**
- Uses `node:20`
- Installs dependencies with `npm ci`
- Runs tests with `npm run test:ci`

**Runs on:**
- Merge Requests
- `sbx`
- `develop`
- `main`

**Behavior:**
- `allow_failure: true` (tests report results but do not block the pipeline in this configuration)

**Why this matters (enterprise practice):**
- Keeps feedback fast for developers
- Ensures minimum quality signals before build/deploy steps

---

## 2 Security Scanning Stage (Snyk)

### `snyk_code_scan`
**Purpose:** Security scanning for both:
- Static code analysis (Snyk Code)
- Dependency vulnerability scanning (Snyk Open Source)

**Runtime:**
- Uses `node:20`
- Installs Snyk tooling and converts JSON output to HTML reports

**Steps:**
- Authenticate to Snyk using a token (`SNYK_TOKEN`)
- `snyk monitor --all-projects` (records project snapshot)
- Run scans and generate reports:
  - `snyk code test` → `snyk_code_results.html`
  - `snyk test --all-projects` → `snyk_results.html`

**Artifacts:**
- Always uploads scan reports (even if scans fail)
- Reports expire in 2 days

**Runs on:**
- Merge Requests
- `sbx`
- `develop`
- `main`

**Behavior:**
- `allow_failure: true` (scan results are visible, and can be enforced later via policy)

**Extra:**
- A script posts the overall scan status back to the Merge Request (feedback loop for reviewers)

**Why this matters:**
- Security checks are integrated into CI, not an afterthought
- Provides MR-level visibility for issues

---

## 3 Build Stage (Environment-Specific Builds)

The build stage produces a `dist/` artifact, with separate builds per environment.

### `build_sbx`
- Runs on branch: `sbx`
- `npm run build:sbx`
- Produces: `dist/` as artifact

### `build_qa`
- Runs on branch: `develop`
- `npm run build:qa`
- Produces: `dist/` as artifact

### `build_stage`
- Runs on tag/branch pattern: `release/rc-*-stage`
- `npm run build:stage`
- Produces: `dist/` as artifact

### `build_prod`
- Runs on tag/branch pattern: `release/rc-*-prod`
- `npm run build:prod`
- Produces: `dist/` as artifact

**Why this matters:**
- Environment-specific configuration is baked at build time
- Artifacts are generated once and passed to deploy jobs

---

## 4 Deploy Stage (AWS S3 via OIDC)

Deploy jobs use AWS deployments without long-lived static credentials.

**Deployment approach:**
- Uses `aws-base` image (AWS CLI available)
- Uses GitLab **OIDC ID Token** (`id_tokens`) with audience `https://gitlab.com`
- Calls `aws sts assume-role-with-web-identity`
- Exports **temporary** credentials (short-lived session)
- Uploads `dist/` to an S3 bucket

### Key Security Pattern: OIDC + Assume Role
Instead of storing AWS keys in CI:
- GitLab issues a signed identity token
- AWS STS exchanges it for temporary credentials (duration ~ 15 minutes)
- This reduces credential leakage risk

---

### `deploy_sbx`
- Trigger: `sbx`
- Deploys `dist/` to SBX S3 bucket
- Sets `index.html` with strict cache control (ensures latest app loads)

### `deploy_qa`
- Trigger: `develop`
- Deploys to QA bucket
- Same caching strategy for `index.html`

### `deploy_stage`
- Trigger: `release/rc-*-stage`
- **Manual** deployment (`when: manual`)
- Depends on `build_stage` (uses the build artifact)
- Used as a controlled release gate

### `deploy_prod`
- Trigger: `release/rc-*-prod`
- **Manual** deployment (`when: manual`)
- Depends on `build_prod`
- (Optional) CloudFront invalidation can be done post-deploy (commented in pipeline)

---

## Branch & Release Strategy Summary

- **SBX:** `sbx` branch → auto build + auto deploy
- **QA:** `develop` branch → auto build + auto deploy
- **Stage:** `release/rc-*-stage` → build + **manual deploy**
- **Prod:** `release/rc-*-prod` → build + **manual deploy**

This provides fast iteration in lower environments and controlled, gated releases for higher environments.

---

## What This Demonstrates

- Enterprise CI/CD structure with stages, artifacts, and gated deployments
- Automated testing and security scanning integrated in CI
- Secure AWS deployments using OIDC + short-lived credentials
- Environment-based build & release strategy aligned with SDLC best practices
