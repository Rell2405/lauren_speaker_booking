# Lauren Green Speaker Booking

A responsive, conversion-focused landing page for Lauren Green's speaking,
facilitation, and leadership development engagements.

## Development

Use Node.js 22:

```bash
npm ci
npm run dev
```

Production checks:

```bash
npm run check
npm run build
```

## GitHub Pages

Hosted site: <https://rell2405.github.io/lauren_speaker_booking/>

Pull requests and feature branches run the verification workflow. Pushes to
`main` deploy to GitHub Pages and then smoke-test the live page and hero image.
The deployment workflow can also be run manually from any branch for a hosted
test deployment. GitHub Pages has one shared environment, so a manual branch
deployment temporarily replaces the currently hosted site.
The repository's Pages source is configured for GitHub Actions.

See [`TECH_STACK.md`](./TECH_STACK.md) for architecture, booking integration,
security, privacy, and launch guidance.
