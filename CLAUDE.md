# Instructions for Claude and developers

This file is read automatically by Claude Code. Keep it current: when you finish a task, update "State of this repo" (date, what changed, what is left) in the same commit.

## State of this repo (updated 2026-11-20)

- IT-hjälparna Nordic AB's site, created from site-template on 2026-11-18. Dev preview: https://it-hjalparna-nordic-ab.dev.booqy.se. Production from `main`.
- Uses site-kit 0.3.0 (`package.json`).
- Checkbox override: end of `src/site.css`, commit `e74d511`.
- `main` and `dev` have no common history. Do not merge or rebase one onto the other without the Booqy owner.
- Site languages (2026-11-20): the language picker is in the top bar through `src/shared/lang.tsx` (appears with the next site-kit release, see Temporary overrides), header CSS compacted for the picker and made right-to-left safe in `src/site.css`, `translate="no"` on brand and contact data, tests in `src/shared/lang*.test.*`.
- Last work by the Booqy owner's Claude session: checkbox visibility (2026-11-19). Nothing else in progress; the repo is free to take over.

## Rules for every BooqyCustomers repo

Booqy is a multi-tenant restaurant SaaS (booking, ordering, catering, marketing). Each `site-<slug>` repo is one restaurant's own website: only the look (layout, pages, styles). Booking, menu, ordering, payment, catering and GDPR pages come from the npm package `@bookingsystemadvancedorg/site-kit` (repo `BookingSystemAdvancedOrg/site-kit`). Read `README.md` and `docs/DATA-RULES.md` before changing anything.

**Branches and merging (avoid conflicts):**
- Only `dev` and `main` exist. Work on `dev`, or on a short-lived branch merged into `dev` the same day and then deleted. Never push directly to `main`; going live is a PR `dev` -> `main` that the Booqy owner approves.
- Before you start, and again before every push: `git fetch origin && git rebase origin/dev`. Keep commits small and focused; push each finished piece at once.
- Push to `dev` deploys to the dev preview (`https://<slug>.dev.booqy.se`); push to `main` deploys to production. Watch the `Deploy` run after every push and fix what you broke.
- `.github/` (workflows, Dependabot) belongs to the Booqy owner: propose changes, do not edit.
- In some repos `main` and `dev` have no common history (they were created separately). Do not try to merge or rebase them onto each other; ask the owner how to go live.

**Data:**
- `dev` only ever uses fake data: the restaurant's dev twin in the dev Booqy Admin (`https://restaurang.dev.booqy.se`) plus the dev-only example data in `src/devPlaceholders/` (only where the repo has it). Stripe is test mode (`4242 4242 4242 4242`).
- `main` only ever uses the restaurant's real production data. Production never shows example data.
- The restaurant enters its own location, menu, opening hours and contact details in its Booqy Admin. Never create or edit that data, not even in dev; if something is missing, rely on the placeholders and note it.
- Delete any test bookings or orders you create in dev right after the test.

**Copy and style:**
- All site copy is Swedish.
- Never use the em dash (U+2014) or en dash (U+2013), not even as `&mdash;` or `&ndash;`. Use a comma, colon, parentheses or a hyphen.
- Check every UI change yourself at phone (390), tablet (820) and desktop (1440) widths, in every theme the site has.
- Never put secrets in the repo (deploys use OIDC; `booqy.json` holds nothing secret).

**Consent checkboxes (owner decision 2026-11-19, applies to every site):**
- The booking terms box ("Jag godkänner bokningsvillkoren") starts unticked; the guest must tick it.
- The marketing consent box also starts unticked, always. A pre-ticked box is not valid consent under GDPR (CJEU Planet49). Never pre-tick it; raise sign-ups only with better wording.
- Both boxes must be visible without hover (fix below).

**site-kit and fixes that every site needs:**
- Anything every site needs belongs in site-kit, not here. A site-kit change also has to be copied to `customer-front-end/packages/site-kit` (CI sync check).
- Sites build from the published site-kit version in `package.json`. Publishing is a tag `vX.Y.Z` on site-kit `main`, done by the Booqy owner; then each site bumps the dependency (Dependabot opens the PR to `dev`).
- New sites need site-kit 0.3.0 or later: guest form posts go through AWS WAF via `/api/*` on the site's own domain.
- Guest links use the restaurant's own domain once it is live (`<slug>.booqy.se` then 301-redirects to it).

**Temporary overrides to remove later:**
- Checkbox visibility (2026-11-19). site-kit up to 0.4.0 drew `.booking-check` checkboxes with CSS variables no theme defines, so the boxes were invisible until hover. Fixed in site-kit `dev` (commit `fa61a10`, not published yet). Until a published site-kit release contains it, each site carries an override (file named below). When the site bumps to that release, delete the override in the same PR and check that the boxes still show.
- Site languages (2026-11-20). Guests can switch the site between Swedish, English, Spanish, Arabic and Persian (Amazon Translate, done by site-kit). The picker shows only on a published site-kit release that contains site languages (the release after 0.4.0 is not out yet); on 0.3.0 `src/shared/lang.tsx` returns null and nothing changes for guests. In the bump PR: check the header at 390, 820 and 1440 in all five languages (light and dark; Arabic and Persian in right-to-left), then replace the bridge by a normal `import { LanguagePicker } from '@bookingsystemadvancedorg/site-kit'` and delete `src/shared/lang.tsx` with its bridge test.

## Languages (sv, en, es, ar, fa)

The site can be switched between Swedish (default), English, Spanish, Arabic and Persian; site-kit translates everything (Amazon Translate). How it works for sites, with the rules for names, one-sentence strings, right-to-left CSS and the header, is described in `site-template` (`CLAUDE.md`, section "Languages"); in this site the pieces are `src/shared/lang.tsx` (bridge), `src/shared/CustomerLayout.tsx` (picker slot `.site-header-lang`, `translate="no"` on brand, location, address, legal name, org number) and the section "Språk" at the end of `src/site.css`.
