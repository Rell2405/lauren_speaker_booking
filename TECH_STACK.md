# Lauren Green Speaker Booking Technology Stack

## Purpose

This document defines the production architecture and engineering standards for
the Lauren Green speaker website. The site markets Lauren's keynote, workshop,
moderation, and facilitation services and will route qualified booking
inquiries to an approved booking workflow.

- **Repository:** `Rell2405/lauren_speaker_booking`
- **Hosting:** GitHub Pages
- **DNS and edge security:** Cloudflare recommended
- **Last reviewed:** 2026-08-31

## Architecture

```text
Visitor browser
  |
  v
Cloudflare DNS, proxy, TLS, DNSSEC, and security headers
  |
  v
GitHub Pages static site
  |
  +--> Approved booking form or CRM endpoint (to be selected)
  +--> Approved analytics provider (optional)
```

The marketing site remains statically generated. A narrowly scoped external
service should own inquiry delivery, spam protection, retention, and access
controls rather than adding a general-purpose application backend.

## Frontend stack

| Purpose | Technology | Policy |
| --- | --- | --- |
| Application framework | Astro 7 | Static output |
| Styling | Tailwind CSS 4 and centralized CSS tokens | Reusable responsive visual system |
| Type checking | TypeScript strict mode | Required |
| Build tool | Vite through Astro | Managed by Astro |
| Browser testing | Playwright | Desktop and mobile routes |
| Accessibility testing | axe-core with Playwright | Block serious and critical violations |
| Unit testing | Vitest | Add when content or configuration helpers are introduced |

Interactive React islands are not currently needed. Add React only when a
feature requires persistent browser state that cannot be delivered with
semantic HTML and small, framework-free scripts.

Exact resolved versions live in `package-lock.json`. This document records
architectural intent rather than duplicating lockfile data.

## Site structure

The first release is a single conversion-focused landing page:

1. Hero and primary booking call to action
2. Speaker biography and leadership credentials
3. Signature speaking topics
4. Stage and facilitation experience
5. Best-fit audiences
6. Booking inquiry form

Section navigation uses in-page anchors so core content remains available as
static HTML.

## Visual system

The visual system is based on the approved reference and uses:

- Deep green, warm gold, blush, cream, and white brand colors
- Editorial serif display typography with a clean sans-serif body face
- High-contrast calls to action and visible keyboard focus treatments
- Responsive portrait and stage imagery
- Subtle motion that honors `prefers-reduced-motion`

Temporary image crops from the supplied concept artwork are stored in
`public/images/`. Replace them before launch with owned, licensed,
high-resolution originals and update image dimensions and alternative text.

## Booking model

The page includes an inquiry form interface, but submission remains disabled
until the booking owner and destination are approved. Do not simulate
successful submissions or silently discard inquiry data.

Before enabling the form:

1. Select an approved form service, CRM, or serverless endpoint.
2. Confirm the recipient and backup notification path.
3. Add bot protection, preferably Cloudflare Turnstile.
4. Publish a privacy notice that states what is collected, why, retention, and
   how a visitor can request deletion.
5. Add explicit success and error states and test delivery end to end.
6. Avoid collecting payment details, government IDs, or sensitive health data.

Suggested inquiry fields are name, email, organization, event type, event date,
audience size, location, budget range, and event goals. Only require fields
needed to qualify an inquiry.

## Recommended production integrations

| Service | Purpose | Data consideration |
| --- | --- | --- |
| GitHub Pages | Static hosting | Public site content |
| Cloudflare | DNS, TLS, security headers, optional Turnstile | Standard request metadata |
| Approved form or CRM provider | Booking inquiry delivery | Contact and event data |
| Privacy-friendly analytics | Conversion measurement, optional | Avoid cross-site profiling |

Any third-party integration must be reflected in the privacy notice and
Content Security Policy.

## Development requirements

| Requirement | Policy |
| --- | --- |
| Node.js | Node 22, declared in `engines` and `.nvmrc` |
| npm | Version compatible with the approved Node release |
| Lockfile | `package-lock.json` committed and reviewed |
| Clean install | `npm ci` |
| TypeScript | Strict Astro configuration |

```bash
npm ci
npm run dev
npm run check
npm run build
npm run preview
```

## CI/CD

Pull requests and feature branches run:

1. Reproducible dependency installation
2. Astro and TypeScript checks
3. Production build
4. Desktop and mobile browser tests
5. Automated accessibility checks
6. CodeQL analysis

Pushes to `main` deploy the static `dist/` artifact through GitHub Pages and
then smoke-test the hosted page and a critical image asset. The workflow can
also be dispatched manually for a hosted branch test.

## Browser security

Apply security headers at the Cloudflare edge. Start from:

```text
Content-Security-Policy:
  default-src 'self';
  img-src 'self' data:;
  style-src 'self' 'unsafe-inline';
  font-src 'self' data:;
  script-src 'self' 'unsafe-inline';
  connect-src 'self' https://APPROVED-FORM-ENDPOINT;
  frame-src 'self' https://APPROVED-TURNSTILE-HOST;
  frame-ancestors 'none';
  object-src 'none';
  base-uri 'self';
  form-action 'self' https://APPROVED-FORM-ENDPOINT;
  upgrade-insecure-requests

X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), camera=(), microphone=()
X-Frame-Options: DENY
```

Replace placeholders with exact origins. Review the policy whenever a form,
analytics, video, calendar, or social integration changes.

## Privacy, accessibility, and performance

- Target WCAG 2.2 AA.
- Support keyboard navigation, visible focus, semantic headings, 200% zoom,
  and reduced motion.
- Test current evergreen Chrome, Edge, Firefox, and Safari.
- Keep core content available without JavaScript.
- Optimize final local images at build time.
- Target Lighthouse scores of at least 90 for performance, accessibility, SEO,
  and best practices.
- Collect only the inquiry data needed for follow-up.
- Never commit booking submissions, credentials, API keys, or private contact
  information.

## Recommended updates before launch

- Replace concept-art crops with licensed high-resolution speaker photography.
- Approve the final `BOOK LAUREN` destination and connect the inquiry form.
- Add privacy and terms pages before collecting inquiry data.
- Confirm the canonical domain and production site URL.
- Add approved testimonials, client logos, and social links only after written
  permission is confirmed.
- Add Open Graph imagery and structured `Person` or `ProfilePage` data.
- Configure automated browser, accessibility, link, and form-delivery tests.
- Define lead ownership, response-time expectations, retention, and deletion.

## References

- [Astro deployment guidance](https://docs.astro.build/en/guides/deploy/)
- [Astro image guidance](https://docs.astro.build/en/guides/images/)
- [GitHub Pages custom domains](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/)
- [Cloudflare Turnstile](https://developers.cloudflare.com/turnstile/)
- [Cloudflare Response Header Transform Rules](https://developers.cloudflare.com/rules/transform/response-header-modification/)
- [OWASP HTTP Headers Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
