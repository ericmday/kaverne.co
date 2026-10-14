# Working on kaverne.co

## Roles and review workflow

- The design agent on Eric's Mac owns `index.html`: copy, layout, typography, colour, motion, interactions, new designs, and desktop/phone previews. It does not manage Cloudflare, DNS, email, hosting, or server code.
- The services agent owns Cloudflare Pages projects `kaverne` and `kaverne-night`, deployments, branch previews, domains, certificates, DNS/email preservation, Pages Functions, Durable Objects, data feeds, seat counts, and storage.
- Design changes happen on branches such as `next`, never directly on `main`. Push the branch for Eric to review at `<branch>.kaverne.pages.dev`. Keep branch previews enabled.
- Merge design changes to `main` only after Eric approves: `main` automatically goes live at `kaverne.co`. The services agent then verifies deployment and live responses.
- When design needs a service, write a brief for Eric to pass to the services agent. When services affect the page, report endpoint, header, or configuration changes to Eric for the design agent.
- The services agent must not change the page's look, copy, or motion unless Eric asks. Small service-wiring changes are allowed; describe them.
- Both agents may update the sections of this shared reference they own.

## Site and design

- This is a single static page: `index.html` contains the HTML, CSS, and JavaScript. There is no package manager, framework, or build step.
- The original night-shift build is preserved on branch `night-shift`, deployed by project `kaverne-night` at `night.kaverne.co`. Keep its `noindex` meta tag. Preserve its near-black background, JetBrains Mono, dim gray text, restrained ember accents, typing stream, and faint letter-based shapes unless Eric requests an archive change.
- The main site may evolve through the design agent's approved branch workflow.
- Keep the mobile layout, reduced-motion behavior, and `hello@kaverne.co` link working. Pressing `.` should summon a faint shape from recent stream text.
- The "in the seats" count depends on claude.ai and is intentionally hidden on the public site. Do not repair or replace it unless requested.
- The stream is currently generated in the browser. Real data through Pages Functions and a Durable Objects seat count are future work, not implied by routine edits.

## Preview and verification

1. Inspect `git status` and preserve unrelated user changes.
2. Make design edits on a review branch, preserving role boundaries above; avoid introducing a framework or build pipeline without a concrete need.
3. Preview with `python3 -m http.server 8000` from the repository and open `http://localhost:8000`.
4. Check desktop and narrow mobile layouts, let several stream lines type out, press `.`, and check browser errors. Test reduced motion when changing animation. Static text or documentation changes do not need a new test suite.
5. Review the diff before committing. Never commit credentials, API tokens, or local authentication files.

## Hosting and deployment

- Repository: `https://github.com/ericmday/kaverne.co`, production branch `main`.
- Cloudflare Pages project: `kaverne`; preview/production fallback: `https://kaverne.pages.dev`.
- Branch previews on `kaverne` stay enabled for all branches: `<branch>.kaverne.pages.dev` (Cloudflare normalizes branch names for the hostname). Share the deployment's exact URL when available.
- Archive project: `kaverne-night`, production branch `night-shift`, hostname `https://kaverne-night.pages.dev`, custom domain `https://night.kaverne.co`. Keep this branch independent of future `main` design updates.
- GitHub integration automatically deploys pushes to `main`. Framework preset is None, build command is empty, output directory is `.`.
- Custom domains: `kaverne.co` and `www.kaverne.co`. Cloudflare has a 301 rule from HTTPS `www` to the bare domain, preserving paths and query strings.
- After an authorized push, verify the new deployment succeeded and corresponds to the pushed commit. `npx wrangler pages deployment list --project-name kaverne` is useful; Wrangler is not installed globally.
- Verify `https://kaverne.co` returns successfully and `https://www.kaverne.co/` redirects to it. If certificate validation is pending, report that explicitly; a successful pages.dev deployment alone does not confirm custom-domain HTTPS.
- GitHub Pages is disabled. Do not re-enable it or recreate `CNAME`. `.nojekyll` is a harmless leftover.

## DNS and email: preserve these

- Registration remains at Namecheap; DNS is managed by Cloudflare on the free plan.
- Nameservers: `derek.ns.cloudflare.com` and `evelyn.ns.cloudflare.com`.
- Web CNAME records for the apex and `www` point to `kaverne.pages.dev` and are proxied.
- Google Workspace email is in use. Keep MX `@` priority `1` pointing to `smtp.google.com.` and TXT `@` equal to `v=spf1 include:_spf.google.com ~all`.
- Preserve the full TXT value at `google._domainkey.kaverne.co` exactly. Read it with `dig +short google._domainkey.kaverne.co TXT`; multiple quoted chunks form one value and must be concatenated without added spaces if entered as a single string.
- Email records are DNS only. Do not remove or alter MX, SPF, or DKIM while editing web DNS. DMARC was absent at migration; adding a policy requires a separate request.
- Cloudflare Email Address Obfuscation must stay off so `hello@kaverne.co` remains plain text in the served page.
- Before any future DNS migration, capture the original email records and verify them on both replacement authoritative nameservers before switching delegation. Recheck public MX, SPF, and DKIM afterward.
- Do not change nameservers, hosting providers, email settings, or paid plans as part of an ordinary page update.
