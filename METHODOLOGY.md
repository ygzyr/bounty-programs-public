# Methodology (generic)

These are generic, non-operational notes. They describe how I approach authorized
bug-bounty work and what I check; they do not contain target-specific paths, payloads,
requests, or reproduction steps.

## 1. Scope and rules first

- Read the program policy, scope, exclusions, and safe-harbor terms before touching
  anything.
- Test only assets you own or that are explicitly in scope; use researcher-owned
  accounts.
- Respect rate limits and stop at the minimum evidence needed to prove impact.
- Prefer local reproduction for open-source findings and never test against a
  program's production infrastructure when the policy forbids it.

## 2. Reconnaissance

- Passive: certificate transparency, public DNS data, search engines, public code and
  documentation, published mobile packages.
- Active: careful, rate-limited requests to in-scope hosts; compare edge and origin
  behavior; look for staging/test surfaces that should not be public.
- Map authentication boundaries, tenant boundaries, and privileged vs unprivileged
  roles before probing business logic.

## 3. Identity, authorization, and session boundaries

- Test object-level authorization (BOLA/IDOR) with two accounts you own: change the
  identifier, keep the session, and check whether the server enforces ownership.
- Test role separation and privilege boundaries; do not assume a hidden UI control is
  an access control.
- Review CSRF/origin checks on state-changing endpoints, especially JSON APIs and
  body-agnostic handlers.
- Review OAuth/OIDC flows: `state` binding, redirect URI allowlists, code exchange,
  and whether tokens are accepted from URLs or GET requests.
- Check logout, session rotation, and cookie scope/`SameSite` behavior.

## 4. Client-side and build output

- Treat source maps, bundle metadata, and build artifacts as potentially sensitive.
- Search browser-delivered JavaScript for credentials, internal hostnames, feature
  flags, and hidden endpoints.
- Review CORS: reflected origins, `null` origins, credentialed requests, and
  preflight behavior.
- Review JSONP and legacy callback parameters; prefer CORS with an explicit allowlist.
- Review `postMessage` handlers for origin checks and data validation.

## 5. Configuration and cloud surfaces

- Look for exposed configuration trees, debug endpoints, directory listings, and
  backup files.
- Check object-storage permissions and signed-URL scope; do not enumerate or download
  third-party data.
- Check whether secrets in documentation, issue trackers, or public repositories are
  still active; never use them beyond a minimal, authorized validity check.

## 6. Reporting

- Lead with impact: what an attacker can read, write, execute, or disrupt.
- Include a minimal, reproducible proof of concept and the exact affected version or
  asset.
- Redact tokens, cookies, PII, customer data, and internal hostnames.
- Respect disclosure timelines; publish only after program permission or coordinated
  disclosure.
- Keep a private archive for unpublished findings and a public index for disclosed
  work.

## 7. Defensive takeaways

- Keep source maps and build metadata out of production, or restrict them.
- Never ship service credentials in browser-delivered code; mint short-lived,
  user-scoped tokens server-side.
- Enforce authentication and object ownership in the application, not only at the edge.
- Allowlist redirect targets, CORS origins, and OAuth callback URLs explicitly.
- Rotate any credential that has ever appeared in a public artifact.
- Scan commits, issues, documentation, and build output for secrets.
