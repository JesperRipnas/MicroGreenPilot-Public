# Security overview

Security is a significant engineering concern in MicroGreenPilot, particularly because authentication, account settings, multi-user data, and administrative controls are central to the product.

This document describes the public design approach only. Detailed security runbooks, environment configuration, implementation-specific attack surfaces, and operational procedures remain in the private development repository.

## Principles

### Server-side authorization

The browser is never considered the authorization boundary.

Protected operations are checked by the API using authenticated server-side context. UI visibility and route guards improve user experience, but do not replace backend authorization.

### Least privilege

Different application roles have different capabilities.

Normal users, administrators, and higher-privilege operational roles are intentionally separated. Higher-risk actions require stronger validation than ordinary product interactions.

### Workspace isolation

Production data is scoped to the authenticated user's authorized workspace/grow space.

References between workspace-scoped entities are validated by the backend so an identifier from another workspace cannot be treated as authorization.

### Defense in depth

Authentication-library primitives are combined with application-owned validation, authorization, persistence constraints, security events, and regression testing.

The design does not assume that a single middleware check or hidden frontend control is sufficient protection.

## Authentication capabilities

The current application work includes:

- email/password sign-up and sign-in
- email verification
- password recovery
- authenticated password changes
- Google authentication
- explicit provider/account linking behavior
- cookie-based sessions
- optional TOTP two-factor authentication
- backup codes
- trusted-device behavior
- session revocation

The exact implementation and sensitive configuration are not published here.

## Credential handling

Credentials and tokens are treated as server-side secrets.

Design expectations include:

- secrets are not stored in frontend configuration
- passwords and one-time authentication values are not logged
- reset and verification flows are time-limited
- sensitive account changes require fresh proof where appropriate
- sessions can be revoked after security-sensitive mutations

## Administrative actions

Administrative and operational capabilities are separated from ordinary product access.

Sensitive changes are designed to be:

- explicitly authorized
- auditable
- protected by stronger verification where warranted
- denied by default when required security state cannot be established

## Security events and observability

Security-related workflows produce structured events useful for investigation and operations.

The logging model is designed to preserve useful context without logging raw passwords, one-time secrets, provider tokens, session cookies, or unnecessarily sensitive user data.

## Secure failure behavior

Security-sensitive operations favor explicit failure over silently weakening a boundary.

Examples of this general philosophy include:

- reject unauthorized cross-workspace references
- do not trust client-provided ownership claims
- do not continue a protected mutation when required validation cannot complete
- distinguish authentication failure from application success states
- avoid exposing internal error details to the browser

## Dependency and CI checks

Dependency and security checks are part of the private CI pipeline.

Automated scanning is treated as one signal rather than as a security guarantee. Security-sensitive workflows are also backed by targeted unit, integration, and end-to-end regression tests.

## Public-repository boundary

The following categories intentionally remain outside this portfolio repository:

- production or staging credentials
- internal environment values
- private CI logs and artifacts
- detailed authentication runbooks
- exact privileged operational procedures
- internal vulnerability discussions
- exploit-oriented implementation notes

That boundary allows the project to demonstrate serious security engineering without turning the portfolio repository into an operational guide to the private application.

## Reporting

MicroGreenPilot is currently a private-development, pre-release project rather than a public hosted service.

If a public deployment or external security-reporting process is introduced later, the public repository can be updated with an appropriate disclosure and contact policy.
