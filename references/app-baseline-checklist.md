# App baseline checklist — the expected things

What a shipped application is *expected* to have before it goes to real
users. Stack- and platform-neutral — mark items `N/A` with a reason
(a static marketing site clears half of this; a SaaS clears different
halves).

Run by **joe** as a completeness pass on "is this app ready" / launch
review. Pairs with the other two launch gates: **david** catches code
holes, **randy** catches statutory ones, this catches *missing things an
app should simply have*. Each item: 🟢 present / 🟡 partial / 🔴 missing /
⚪ N/A — with evidence, not vibes.

## Identity & lifecycle

1. **Visible version/build** — somewhere in-app AND in every diagnostic
   artifact. Support questions start with "what version are you on."
2. **First-run onboarding** — a guide, welcome, or at minimum empty
   states that teach instead of dead-ending.
3. **Settings home** — preferences grouped in one discoverable place;
   no config that requires editing files or re-installing.
4. **Update path** — installed apps need an updater or a stated upgrade
   story; web apps get this free. Uninstall/cleanup story: what data
   lives where, what survives removal.

## Supportability

5. **Centralized logging** — one service, DRY, rotating so it can't grow
   forever, opt-in debug verbosity, and it must never crash the host.
6. **Crash & error capture** — global/unhandled exception hook with
   enough context (breadcrumbs, timestamps, stack) to reconstruct the
   failure without the customer narrating it.
7. **Support report** — one-click diagnostic package the customer can
   review before sharing. Contract:
   - Collect by **allowlist only** — versions, counts, integrity checks,
     logs. Never record contents, credentials, tokens, paths unless
     explicitly allowlisted.
   - Sanitize anyway — pattern-scan what the allowlist returns; flag
     suspect strings for the customer rather than trusting regex.
   - Customer reviews the contents on screen; nothing transmits without
     an explicit send. Copy-file + mailto is the honest v1.
   - Diagnostics decoupled enough to run when the main UI is broken.
   (Full spec pattern: the pasted "Privacy-First Diagnostics" prompt —
   archive in this repo's `references/` if a project adopts it verbatim.)
8. **Health checks** — cheap internal status: datastore reachable,
   required config present, background jobs running. A status surface
   helps; silent failure is the enemy.
9. **Support route** — a visible "get help" path in-app (email, site,
   docs) even if it's just a mailto.

## Data & integrity

10. **Backup/export** — user data can leave the app in a portable form.
    Locked-in data is a trust failure and usually a legal one.
11. **Deletion semantics** — deletes are undoable or tombstoned; ids and
    links don't break when things move (project AGENTS.md owns the
    specifics — scott's lane).
12. **Degradation** — offline/permission/failure modes degrade to
    something, not a blank screen or a hung spinner.

## People-facing surfaces

13. **Legal surfaces** — privacy policy, terms, license file. Run
    randy's launch gate; categories-over-vendor-names in the policy.
14. **Accessibility floor** — keyboard navigable, contrast passes,
    respects reduced-motion, 44px touch targets. Mira/Jerry verify.
15. **Error copy** — failures tell the user what happened and what to
    do next, in the product's voice. No raw stack traces in UI.

## Verification

16. **Test suite exists** — and Jerry has run it on the changed area.
17. **Launch gates passed** — david (security), randy (legal), this
    list. All three, not one.

## Verdict

```markdown
## Baseline pass — <date>
| Area | Item | Mark | Evidence / gap |
...

Verdict: Complete / Gaps to close (list) / Blocked on <named item>
```

Items an app deliberately omits are fine — record the omission and the
reason in the verdict so the next pass doesn't re-litigate it.
