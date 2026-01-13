# Auth Flow (OAuth2 / OIDC + PKCE) — High Level

This describes a modern secure login flow for web applications (SPA + API).

## Actors
- User (browser)
- Frontend (React / Vue)
- Authorization Server / Identity Provider (IdP)
- Backend API (Node.js)
- Resource Server (protected APIs)

## Flow
1) **User clicks “Sign in”** in the frontend.
2) Frontend generates **PKCE**:
   - `code_verifier` (secret kept in browser memory)
   - `code_challenge` (hashed form sent to IdP)
3) Frontend redirects user to IdP `/authorize` with:
   - `client_id`, `redirect_uri`, `scope`, `state`
   - `code_challenge`, `code_challenge_method=S256`
4) IdP authenticates user (password/MFA/SSO policies).
5) IdP redirects back to frontend with an **authorization code**.
6) Frontend calls IdP `/token` with:
   - `authorization_code` + `code_verifier`
7) IdP returns:
   - **access_token** (short-lived)
   - **refresh_token** (longer-lived, optional)
   - (optional) **id_token** for user identity claims
8) Frontend calls Backend API with `Authorization: Bearer <access_token>`.
9) Backend validates token (signature, expiry, audience, issuer) and enforces **RBAC**.
10) When access token expires:
   - Frontend uses refresh token (or silent auth) to obtain a new access token.

## Security Notes
- PKCE prevents interception of authorization codes.
- Access tokens should be short-lived.
- Refresh tokens should be stored securely (prefer httpOnly secure cookies where applicable).
- RBAC + least privilege should be enforced on every sensitive endpoint.
