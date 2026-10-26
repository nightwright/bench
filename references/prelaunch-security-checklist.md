# Pre-Launch Security Checklist — vendored & generalized

Source: adapted from Prajwal Tomar's "security check before every
launch" (x.com/@PrajwalTomar_, Oct 2026) — generalized from its
Supabase/React shape to any stack. Synced 2026-10-26.

Scope note: run the steps that apply to the surface. A static site
scores most steps N/A — say so explicitly rather than padding. The
steps that always matter: secrets, headers, abuse/cost on any
endpoint that can spend money.

## Step 1 — Legal & data basics

- Privacy policy exists and matches what the app actually collects
  (not a template that lies).
- Where user data lives: DB region, hosting region, and every third
  party that touches it (email, analytics, payments, AI APIs).
- No plain-text passwords, no user data exported to personal
  channels (own email, random sheets).
- License check: no GPL-style dependency that could force
  open-sourcing the product.

## Step 2 — Data layer

- Authorization per record: every table/entity holding user data has
  access rules (RLS policies, query filters, middleware). "Any
  authenticated user reads all rows" is a fail. RLS on with zero
  policies blocks everyone; RLS off on an exposed table is the
  danger — check both.
- Test, don't trust: request private data logged-out (blocked),
  then as user A request user B's data (blocked, both directions).
- Server-side validation on every write — client-side checks are
  UX, not security.
- Errors generic to the user, detailed in server logs only.

## Step 3 — Auth enumeration

- 5x wrong password → throttled/locked; message must not reveal
  whether the account exists.
- Password reset for unknown email → identical response to a
  registered one.
- Verification link clicked twice → nothing breaks.
- Signup with an already-registered email → no existence leak.

## Step 4 — Audit pass

- Security headers: CSP, frame-ancestors/X-Frame-Options, nosniff,
  referrer-policy, HSTS where applicable. Check free at
  securityheaders.com.
- OWASP top-ten pass over the actual surface.
- Secrets: no secret keys in frontend code, bundles, or git history.
  Public keys (Stripe pk_*, publishable/anon keys) are fine;
  sk_*, service-role, and AI API keys never reach the browser.
  A secret that ever hit a public repo → rotate first, ask later.
- Data shipped pre-auth: does the login/public page itself carry
  private records in source? (The franchise-dashboard failure mode.)

## Step 5 — Abuse & cost

- Rate limits on login, signup, reset, and every endpoint that calls
  a paid API.
- Bot protection on public forms — token verified server-side, not
  just rendered client-side.
- CORS locked to own domains.
- Spending caps + alerts set inside each paid-API dashboard
  (database, AI, email — wherever a spike bills you).

## Step 6 — Scanner

- Run the stack's scanner (npm audit, osv-scanner, GitHub dep
  alerts, container/dep scanners) and triage every flag.

## Verdict

Write `launch-verdict` output: **pass** or **fix first**, one result
per check, plus an explicit "not covered" list (this is a first
pass, not a full pentest). Dynamic checks (live login probing,
runtime requests) belong to l33t — david's half is static.
