# PetID — Web

An ID card for your pet — a profile with its photo, species, breed, age and food, a
vaccination calendar that reminds you when the next shot is due, seasonal reminders such as
tick season, and its vet's details.

The web platform repository of the PetID container. `../CLAUDE.md` governs product
behavior, domain vocabulary, user-facing copy, and releases. This file governs how the site
is built and served.

**This site exists for one job: the public pages the App Store and Google Play require.**
It is not an app and not a mirror — it implements none of PetID's behavior, has no layers,
and `../tools/parity-check.py` does not measure it. A feature request that lands here is
almost certainly meant for the apps.

**This repository is public.** GitHub Pages on the free plan serves only public
repositories, so everything committed here — this file included — is readable by anyone.
Nothing that is not meant for the world goes into it: no drafts of unreleased features, no
internal notes, no credentials.

## The tree

```
petid-web/
├── CLAUDE.md
├── .nojekyll        Pages serves the files as they are
├── index.html       what PetID is, in two lines, and the links
├── privacy.html     the privacy policy — rendered by ../tools/make-web-pages.py from ../docs/privacy-policy.md
├── support.html     the support page — rendered the same way from ../docs/support-page.md
└── style.css        the one stylesheet: system fonts, light and dark
```

`privacy.html` and `support.html` are never edited here: their sources are in the container's `docs/`, and
`make-web-pages.py` refuses to render while a placeholder (the contact email, the date) is left.

**This tree is the single source of truth for this repository's structure. When a file or
folder is added, removed, or renamed, update it in the same change.**

## Status

The index and the stylesheet are written; the privacy and support pages are drafted in `../docs/` (2026-10-11,
M7b) and wait for the owner's contact email, a lawyer's reading and the owner's yes before they are rendered,
pushed and Pages is turned on.

**Stack decided:** plain HTML and CSS, no build step. What is in the repository is exactly
what is served.

**Host decided:** GitHub Pages, deployed from branch `main`, path `/`.

**First task:** write the pages below, add an empty `.nojekyll` at the root so Pages serves
files as they are rather than running them through Jekyll, then turn Pages on:

```bash
gh api -X POST repos/graboosky/furcard-web/pages -f 'source[branch]=main' -f 'source[path]=/'
```

## The pages the stores require

| Page | Required by | When |
|---|---|---|
| Privacy policy | App Store Connect and Google Play | every app, before the first submission |
| Support | App Store Connect (Support URL) | every app |
| Terms of use | App Store Connect | only if PetID sells an auto-renewable subscription — Apple's standard EULA may be linked instead |
| Account deletion | Google Play | only if PetID lets a user create an account |

Privacy policy: `privacy.html`. Support: `support.html`. A URL pasted into a store listing is permanent in
practice — renaming a page later means a 404 in front of review and a metadata edit in both stores.

**The privacy policy must say what the app actually does with what the user gives it** —
pet photos, health records, the vet's contact details, reminders. Write it from
`../docs/domain.md` once the data a feature stores is decided there, and update it in the
same release as any feature that changes what is collected or where it goes.

## Domain

| What | Value |
|---|---|
| Production URL | `https://graboosky.github.io/furcard-web/` |
| DNS / registrar | none — the domain is GitHub's. A custom domain is a later decision (a `CNAME` file plus DNS). |
| Host | GitHub Pages |

Every URL a store listing links to must return 200 before any store submission — review
fetches them, and a 404 costs a review cycle. `../docs/release.md` puts web first in the
order for this reason. Check with the verb review uses — a `GET`, not a `HEAD`, which a CDN
can answer differently:

```bash
curl -s -o /dev/null -w '%{http_code}\n' https://graboosky.github.io/furcard-web/<page>.html
```

## Build & test

There is no build. Preview locally from the repository root:

```bash
python3 -m http.server 8000
```

then open `http://localhost:8000/`. A push to `main` is a deploy.

## Generated content

If any page here is generated from another source — legal text, pricing, release notes —
say so **on the page and here**, with the command that regenerates it. A file silently
overwritten by an export script is a file someone will edit by hand and lose.

- None yet.

## Conventions

- Every colour, size and font is defined once, as a custom property in one stylesheet, and
  pages use the names. The apps' `designSystem/` rule, in the only form a static site needs.
- User-facing copy comes from `../docs/domain.md`. Writing a second version of a sentence
  here is how the product starts saying two different things.
