# Launch legal checklist

Pre-launch legal exposure pass. Stack-neutral — mark steps that don't
apply `N/A` with a one-line reason (a static site with no accounts,
email, uploads, or payments clears most of this by having no surface).

Run by **randy** on "launch check" / before first public release, or when
a feature adds one of these surfaces. Pairs with david's security gate
(`prelaunch-security-checklist.md`) — security catches the code holes,
this catches the statutory ones.

Verdict format at the end. Every item gets a mark (🟢 ok / 🟡 partial /
🔴 missing / ⚪ N/A) plus file or UI evidence — no unverified "fine."

## 1. Kids — COPPA age gate

- Who uses this? If the product could reasonably draw under-13s, or
  signup doesn't distinguish age at all, exposure exists.
- Fix paths (pick one): a minimum-age clause in the ToS ("not for users
  under 13/16"), an age check at signup, or actual COPPA compliance
  (verifiable parental consent — expensive, only if kids are the market).
- Fines run to ~$50K+ per child per violation. "We didn't know" isn't a
  defense once signup collects no age signal.
- N/A example: B2B tools marketed and priced for businesses.

## 2. Externally loaded assets — fonts, CDNs, embeds

- Does any page pull fonts, scripts, images, or iframes from a third
  party that sees the visitor's IP? (Google Fonts off Google's CDN is
  the classic — a Munich court awarded a visitor €100 under GDPR.)
- Map every external origin in `<head>` and CSS. Each one is a data
  transfer to that vendor.
- Fix: self-host the asset, or use a vendor/CDN arrangement covered in
  the privacy policy with a lawful basis.
- If any EU traffic is plausible, the privacy policy must name the
  transfers.

## 3. Session replay & behavioral recording — CIPA and friends

- Does the stack include session replay (Hotjar, FullStory, LogRocket,
  Clarity), chat widgets that read keystrokes, or pixel/event tools that
  capture form input?
- California's CIPA treats recording what users type without consent as
  wiretapping — ~$5K per violation, and plaintiffs' firms trawl for it.
  Other states have similar statutes.
- Fix: disclose it in the privacy policy AND get consent before the
  recorder starts (banner or settings opt-in that actually gates the
  script — disclosure alone has lost cases).
- N/A example: no analytics beyond server logs; privacy-first tools
  (Plausible) that don't record sessions or input.

## 4. Email — CAN-SPAM

- Any email the product or studio sends that isn't purely transactional:
  launch announcements, newsletters, "we're live" blasts.
- Requirements per message: a working unsubscribe mechanism AND a
  physical postal address (a registered agent or PO box counts — don't
  publish a home address).
- Also: truthful From/subject lines, honor unsubscribes promptly
  (10 business days statutory, same-day in practice).
- ~$50K+ per email. A Mailchimp/Resend template handles the mechanics —
  the postal address is the part founders skip.

## 5. Subscriptions — auto-renewal disclosure

- Anything that bills on a recurring basis: the renewal terms (price,
  cadence, how to cancel) must sit immediately next to the
  subscribe/pay button — not behind a link, not just in the ToS.
- California's ARL and the FTC's click-to-cancel rule both bite; some
  states treat undisclosed renewals as gifts the merchant must refund.
- Cancel must be as easy as subscribe — same channel, similar effort.
- N/A example: one-time sales, free products, B2B negotiated contracts.

## 6. User uploads — DMCA safe harbor

- Can users upload files, images, comments, or anything that could be
  someone else's copyrighted work?
- §512 safe harbor requires a designated agent registered with the
  Copyright Office — $6, one form, at copyright.gov/dmca-directory.
- Also needs: a DMCA policy page (takedown contact, counter-notice
  process) and a repeat-infringer clause in the ToS.
- Without the agent: no safe harbor, statutory damages to ~$150K per
  willfully infringed work.
- N/A example: no user-generated content anywhere.

## 7. Marketing claims vs shipped reality

- Hal's copy and the site's claims must not outrun legal posture —
  "private," "secure," "anonymous," "compliant," "your data never
  leaves..." Each claim needs the technical reality to back it or it's
  an FTC Section 5 problem. Cross-check with david's findings.

## 8. Policy audit — promises and named parties

Applies to privacy policies and terms pages, especially AI-drafted ones —
generators reliably write in promises nobody approved and enumerate every
vendor in the stack.

- **Extract before editing.** Quote every promise, guarantee, named
  person, and named vendor in the document; present the list for owner
  confirmation. Change nothing until the owner rules on each item.
- **Promises must match the build.** "We never sell," "deleted within 24
  hours," "encrypted end-to-end," "we retain nothing" — each one is
  enforceable-ish language that must be true forever, under every failure
  mode, or it's an FTC Section 5 problem. Weaken to what's actually
  shipped and committed.
- **Categories over vendor names.** GDPR Art 13/14 and CCPA require
  disclosing *categories of recipients* — "hosting provider," "payment
  processor," "email service" — not necessarily brands. Category
  language keeps the policy true across stack changes and doesn't hand
  anyone a vendor map. Name names only where a law or the vendor's own
  terms require it.
- **Staleness.** If the policy names tools, flag every name against the
  current stack — a vendor that shipped yesterday invalidates the
  document until it's amended.

## Verdict

```markdown
## Launch legal pass — <date>
| Item | Mark | Evidence / gap |
| 1 Kids / COPPA | | |
| 2 External assets | | |
| 3 Session replay | | |
| 4 Email / CAN-SPAM | | |
| 5 Auto-renewal | | |
| 6 DMCA agent | | |
| 7 Claims vs reality | | |
| 8 Policy audit | | |

Verdict: Clear / Fix-first / Needs licensed counsel
```

`Needs licensed counsel` when any item turns on a specific jurisdiction,
the product's core business is one of the regulated surfaces (kids,
health data, money), or a real lawsuit/enforcement question exists —
randy flags, a human attorney answers.
