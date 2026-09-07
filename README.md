# Outrush — website

Two static pages: the landing page, and one page carrying both the privacy policy and the terms of use. No build step, no dependencies, no JavaScript.

It exists to satisfy four separate requirements at once: AppLovin's **Website** field at signup, the **privacy policy URL** that AppLovin's consent flow requires (and that the game refuses to run ads without), the privacy URL on both store listings, and a place to point players.

## Layout

```
Outrush-website/
├─ index.html     landing page
├─ privacy.html   privacy policy AND terms of use, in that order
├─ styles.css     shared — palette and geometry lifted from the game
└─ favicon.svg
```

Both legal documents live on `privacy.html` deliberately. The stores and AppLovin all ask for a *privacy policy* URL, so that is what the filename says and that is what loads first; the terms sit below it at `privacy.html#terms`, which is a normal anchor and jumps straight there.

The design is not an approximation of the game's look, it is the same values: colours come from `OutrushRN/src/ui/theme.js`, the hero board's wall, pit, gutters and gate notches come from `Board.js`, and the block radius and bevel come from `Block.js`. The game uses the platform system font at weights 700–900 with tight negative tracking, so the site loads no webfont — that would look *less* like the game, not more.

## Before you publish

Nothing is left to fill in — the contact address and publisher name are in place, and there is no governing-law clause to complete.

One thing in `index.html` is still a placeholder by necessity: the four **App Store / Google Play** buttons are `aria-disabled` and link nowhere. When the app is live, swap them for the official badge artwork from Apple and Google — both stores require their own badge images and forbid home-made ones.

## Publishing on GitHub Pages

Free, and stable enough to be the URL a store listing points at for years.

```bash
git init && git add -A && git commit -m "Outrush website"
git branch -M main
git remote add origin https://github.com/developedbymax/outrush.git
git push -u origin main
```

Then in the repository: **Settings → Pages → Source: Deploy from a branch → `main` → `/ (root)` → Save**. It goes live in a minute or two at:

```
https://developedbymax.github.io/outrush/
https://developedbymax.github.io/outrush/privacy.html
https://developedbymax.github.io/outrush/privacy.html#terms
```

A custom domain is optional and set on the same screen.

## Then wire the URLs into the game

In `OutrushRN/.env`:

```
EXPO_PUBLIC_PRIVACY_POLICY_URL=https://developedbymax.github.io/outrush/privacy.html
EXPO_PUBLIC_TERMS_URL=https://developedbymax.github.io/outrush/privacy.html#terms
```

Then rebuild with `npx expo start --clear`. **The `--clear` is not optional** — `EXPO_PUBLIC_*` values are inlined at build time and Metro caches the transform, so without it the next build can still carry the old empty values and ads will silently never load.

## Previewing it locally

Opening `index.html` straight from Finder works, but some previews (including editor side-panels) render a local file as a static snapshot that cannot follow links. If page-to-page navigation appears dead, serve it properly instead:

```bash
python3 -m http.server 8787
```

Then open `http://localhost:8787/`.

## A note on the legal text

These are careful, honest drafts written specifically against what this game actually does — no server, no account, local SQLite, AppLovin for ads, store-processed purchases, and stars that are genuinely lost on uninstall. They are not a lawyer's work. If the game earns enough to matter, or you take it into a market with its own rules, have someone qualified read them.

**There is deliberately no governing-law clause.** Without one, a dispute falls to whatever court would normally take it, which for a consumer is usually their own country — the same place most consumer-protection law would have sent it anyway. That is a normal choice for a free game and costs nothing here; it is worth revisiting only if the game ever carries real revenue.

Two things to keep true rather than to fix once:

- If you enable crash reporting (`EXPO_PUBLIC_SENTRY_DSN`), privacy §4 already covers it. If you never enable it, that clause over-discloses, which is harmless — but you may prefer to delete it.
- If you ever add an ad network beyond AppLovin's mediation, or add analytics, privacy §2 stops being accurate. Update it in the same change, not afterwards.
