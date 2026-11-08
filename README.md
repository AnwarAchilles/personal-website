# Anwar Achilles

**Forged through Rune. Shaped with Exura. Shipped on Vercel.**

The personal profile website of Anwar Achilles — **Welcome**, **Profile** (bio & career), **Skill**, plus **Login** and a **Dashboard** to manage the content — built with Rune and the Exura series, deployed on **[Vercel](https://vercel.com)**: a static page in `public/`, an API in `api/` that runs as a Vercel PHP function. Write it in many small files in `src/`, and Rune bundles them into two.

**[Explore Rune ↗](https://github.com/devneet-id/rune)**
**[Explore Exura ↗](https://github.com/devneet-id/exura)**

![The page on desktop](public/assets/snapshot.jpg)

<p align="center"><img src="public/assets/snapshot-mobile.jpg" alt="The page on mobile" width="260"></p>

---

## Made with Rune

This app holds three **runites**, all engraved from the origin runes of **[Rune](https://github.com/devneet-id/rune)**, a lore-driven, modular concept beneath PHP:

| Runite | Job |
| ------ | --- |
| `cast` | builds, serves, watches and stores the app |
| `exura` | installs and removes the Exura series from the CDN |
| `vercel` | logs in and pushes the app to Vercel |

| Rune | Role in this app |
| ---- | ---------------- |
| Aether | opens and closes the flow |
| Chanter | registers the casts |
| Whisper | speaks to the user and asks |
| Weaver | fills the templates while bundling |
| Crafter | bundles `src/` into `public/index.html` and `api/index.php` |
| Forger | fetches, writes and removes files |
| Cipher | gives every bundle its hash |
| Keeper | remembers the shards in `.echoes` |
| Specter | serves, watches, and runs the Vercel CLI beside the flow |

Want to engrave your own? Start from the **[Rune repository](https://github.com/devneet-id/rune)**.

### Requirements

* PHP 8.1+ with `curl` (or `allow_url_fopen`)
* Composer
* [Vercel CLI](https://vercel.com/docs/cli) *(to deploy)*: `npm i -g vercel`

### Install

```bash
composer install
php exura setup
php cast start
```

Then open `http://127.0.0.1:8000`, press **Login** and enter the password (`@Arc123` until you change it with `php cast password`).

---

## The App

```
src/
├── exura/            exura.css · exura.js · exura.php (php exura setup)
├── index.head.html   goes into <head>
├── index.html        goes into <body>
├── index.css         becomes <style>
├── index.js          becomes <script>
└── index.php         the API
public/
├── index.html        the page (php cast build)
└── assets/
    ├── background.jpg
    └── snapshot*.jpg   the pictures of this README
api/
└── index.php         the API (php cast build), a Vercel function
vercel.json           the routes and the PHP runtime of Vercel
router.php            the same routes, locally (php cast serve)
```

`php cast build` sparks two items:

| Item | From | To | Holds |
| ---- | ---- | -- | ----- |
| `frontend` | `src/exura/*`, `src/index.*` (html, css, js) | `public/index.html` | the page, with ExuraCSS and ExuraJS inside |
| `backend` | `src/exura/exura.php`, `src/index.php` | `api/index.php` | the API, with ExuraPHP inside |

* Edit only `src/`. `public/index.html` and `api/index.php` are made by `php cast build`.
* Put images and fonts in `public/assets/`. The page reads them as `assets/...`.
* **The background** is `public/assets/background.jpg`. Drop your own image there, no rebuild needed.

| Exura part | In this app |
| ---------- | ----------- |
| ExuraCSS | layout as attributes (`flex`, `size`, `misc`, `button`) and dark mode with `body[dark]` |
| ExuraJS | one class per page (`Home`, `Login`, `Dashboard`), each a template string turned into DOM by `element()`, with its own `<exura-*>` module |
| ExuraPHP | `POST /api/login`, `bio`, `career`, `item`, `schema` and `ai`, answered as JSON; any other `/api/*` answers `404` |

### The routes

`vercel.json` sends every request to the right place:

| Request | Goes to |
| ------- | ------- |
| `/api/*` | `api/index.php` (runtime `vercel-php`, with `api/*.txt` included) |
| `/profile`, `/skill`, `/login`, `/dashboard/*` | `/index.html` of `public/` (the page picks itself by path) |
| `/*` | the files of `public/` (`outputDirectory`) |

Locally, `php cast serve` runs `router.php`, which reads the same `vercel.json` on every request — change the routes once, both sides follow. `router.php` and `src/` are left out of the deploy by `.vercelignore`.

### The pages

Every template lives in `src/index.html`; every `<exura-*>` is one ExuraJS module made of a class. A module has no constructor: `static async start()` brings it to life and routes itself.

```
<exura-pages>          Pages              loads the public side
  <exura-home-nav>       PagesHomeNav
  <footer>
  <exura-home>           PagesHome          /            welcome
  <exura-profile>        PagesProfile       /profile     bio & career
  <exura-skill>          PagesSkill         /skill       the items of category skill
  <exura-login>          PagesLogin         /login       (guests only)
<exura-dashboard>      Dashboard          loads the private side, guards the session
  <exura-dashboard-nav>  DashboardNav
  <exura-dashboard-home> DashboardHome      /dashboard
  <exura-bio>            DashboardBio       /dashboard/bio   (Neon · table bio)
  <exura-career>         DashboardCareer    /dashboard/career   (Neon · table career)
  <exura-item>           DashboardItem      /dashboard/item     (Neon · table item: skill, follow)
```

`src/index.js` reads top to bottom: **I · FUNCTION** (`element`, `go`, `session`, `sessionSave`, `logout`, `area`, `request`, `countdown`) → **II · CLASS** (the modules, each followed by `Exura.module(Class)`) → **III · DOMContentLoaded** (`Pages.start()`, `Dashboard.start()`).

ExuraJS takes the tag from the class name, so `static name = 'home'` binds `PagesHome` to `<exura-home>`.

### Login

One password, for content management only — no username.

* The `password_hash` of the password lives in the env **`ADMIN_PASSWORD_HASH`**: an env of Vercel, and a line of `.env` locally (never committed nor deployed).
* Change it with the cast, it asks twice (or takes `--new`) and writes `.env`; then send it to Vercel and deploy:
  ```bash
  php cast password
  php cast password --new=your-password
  php vercel password                                  # ADMIN_PASSWORD_HASH of .env -> Vercel (production)
  php vercel push
  ```
* **Migration from `api/credentials.txt`** (the old place, same password):
  ```bash
  php cast password --migrate                          # the hash of api/credentials.txt -> .env
  php vercel password
  php vercel push
  ```
  Log in on the deploy; when it works, delete `api/credentials.txt`. Until then the API still reads the file when the env is missing.
* The token is signed with HMAC-SHA256 by the env `APP_SECRET`, or by the hash itself — a new password ends every open session. It lives 2 hours (`TOKEN_LIFETIME` in `src/index.php`).
* The session is kept in `sessionStorage`: closing the tab logs out.

### Content

The content lives in **[Neon](https://neon.tech)** (serverless Postgres), one table per content. The tables are written in `SCHEMA` (`src/index.php`); the dashboard creates the missing ones with **Generate schema** (`POST /api/schema`).

| Table | Holds |
| ----- | ----- |
| `bio` | one row (`id = 1`): `name`, `alias`, `profession`, `domicile`, `birth` (date), `updated` |
| `career` | `id` (identity), `started`, `ended` (date, null = Current), `title`, `skills` (jsonb: ids of `item` with category skill), `company`, `duties` (jsonb), `updated` |
| `item` | `id` (identity), `category` (`skill` or `follow`), `name`, `link`, `icon` (a name or an image URL), `updated` |

A table that changes after it exists gets an entry in `UPGRADES` (a check and the SQL that brings it up to date); the dashboard shows it as *waits for an upgrade* and **Generate schema** runs it.

* **Connection:** `DATABASE_URL` (the connection string of Neon, `postgresql://…?sslmode=require`).
  On Vercel: *Settings → Environment Variables* (the Neon integration sets it too). Locally: a `.env` beside `cast`, never committed nor deployed:
  ```
  DATABASE_URL="postgresql://user:password@ep-xxx.region.aws.neon.tech/neondb?sslmode=require"
  ```
* **AI chat:** `GROQ_API_KEY` (a key of [Groq](https://console.groq.com/keys)), the same way: a Vercel env (`vercel env add GROQ_API_KEY`) and a line of `.env` locally. Never in the code. `POST /api/ai` with `{ message, history }` answers `{ reply }` (model `openai/gpt-oss-20b`); the character is `AI_TUNE` in `src/index.php`, the facts come from Neon on every question. Without the key it answers `503`.
* PHP needs `pdo_pgsql` (on Windows: `extension=pdo_pgsql` in `php.ini`). Vercel's PHP runtime has it.
* No database: the content routes answer `503`. Nothing is checked before a query: when Postgres says a table or a column is missing, the API generates the schema itself once and runs the query again. The dashboard reads the schema only on **Check schema**.

| Request | Who | Does |
| ------- | --- | ---- |
| `GET /api/schema` | with the token | every table: does it exist, how many rows |
| `POST /api/schema` | with the token | create the missing tables |
| `GET /api/bio` | everyone | the bio, or `null` |
| `POST /api/bio` | with the token | create: `name` (required), `alias`, `profession`, `domicile`, `birth` (YYYY-MM-DD) |
| `PUT /api/bio` | with the token | update, the same fields |
| `DELETE /api/bio` | with the token | delete the bio |
| `GET /api/career` | everyone | the career list, newest first (Current on top) |
| `POST /api/career` | with the token | add: `title`, `company` (required), `skills` (ids of items, category skill), `start` (YYYY-MM), `end` (YYYY-MM, empty = Current), `duties` (one per line) |
| `PUT /api/career` | with the token | update the entry `id`, the same fields |
| `DELETE /api/career` | with the token | delete the entry `id` |
| `GET /api/item` | everyone | the items by category then name; `?category=skill` or `follow` narrows it |
| `POST /api/item` | with the token | add: `category` (`skill`/`follow`), `name` (required), `link`, `icon` |
| `PUT /api/item` | with the token | update the item `id` |
| `DELETE /api/item` | with the token | delete the item `id` |

* Vercel's files are read-only, so the content never lives in files: the dashboard writes to Neon, online and locally alike.

---

## Registered Casts

### cast

| Cast | Description |
| ---- | ----------- |
| `build` | Bundle `src/` into `public/index.html` and `api/index.php`, then store. |
| `serve` | Serve like Vercel on `127.0.0.1:8000`. |
| `watch` | Rebuild when `src/` changes. |
| `start` | Build, then serve and watch. |
| `store` | Keep the assets as shards. |
| `password` | Set the login password (`--new`, or asks twice). |

### exura

| Cast | Spells | Description |
| ---- | ------ | ----------- |
| `setup` | `--dist` `--tags` `--install` | Set where and which version, then install. |
| `css` | `--install` `--remove` `--tags` `--part=clip` | Install or remove `exura.css` (or `exura-clip.css`). |
| `js` | `--install` `--remove` `--tags` | Install or remove `exura.js`. |
| `php` | `--install` `--remove` `--tags` | Install or remove `exura.php`. |

* Keep Exura in `src/exura/`: both items bundle it from there.
* `--tags` is a tag (`1.1.0`) or a branch (`1.x`, the latest of 1.x). Without it, `EXURA_TAGS` is used.

### vercel

| Cast | Description |
| ---- | ----------- |
| `link` | Log in to Vercel. |
| `push` | Publish to Vercel (`vercel --prod`). |

---

## Try it

Run every command inside this folder.

**The main screens** — every cast with its echo:

```bash
php cast
php exura
php vercel
```

**Exura** — install the series:

```bash
php exura setup                                        # asks: where? install now?
php exura setup --dist=src/exura/ --tags=1.1.0 --install
```

**Build and develop** — `serve` and `watch` run until **Ctrl+C**:

```bash
php cast build
php cast serve                                         # http://127.0.0.1:8000, like Vercel
php cast watch                                         # rebuild on every change in src/
php cast start                                         # build, then serve & watch in their own windows
```

**The API** — locally, then on Vercel:

```bash
curl -X POST "http://127.0.0.1:8000/api/login" -H "Content-Type: application/json" -d "{\"password\":\"@Arc123\"}"
curl "http://127.0.0.1:8000/api/bio"
curl "https://<your-app>.vercel.app/api/career"
```

**Ship** — to Vercel:

```bash
php vercel link                                        # once: log in
php cast build
php vercel push                                        # vercel --prod
```

**Share** — the runites and their shards as one relic:

```bash
php cast store
php cast reliquary --runite-relic                      # cast -> cast.rune
```

---

## What you learn

| Runite · Cast | Lesson |
| ------------- | ------ |
| `cast build` | Crafter: two items from one `src/`, each with its own seed |
| `cast serve`, `watch`, `start` | Specter: a dev server with a router, an observer, casts beside the flow |
| `cast store` | Keeper: shards travel with the relic |
| `exura setup`, `css`, `js`, `php` | Whisper asks; Forger fetches, writes and removes |
| `vercel link`, `push` | Specter runs another CLI beside the flow |

**A web app is many files, Rune makes it two, Vercel ships them.**

---

## References

* [Rune](https://github.com/devneet-id/rune) — Application foundation and tooling.
* [Rune Lore](https://github.com/devneet-id/rune-lore) — The full documentation, from the void to the beyond.
* [ExuraCSS](https://github.com/devneet-id/exuracss) — Styling framework.
* [ExuraJS](https://github.com/devneet-id/exurajs) — JavaScript framework.
* [ExuraPHP](https://github.com/devneet-id/exuraphp) — PHP framework.
* [Vercel](https://vercel.com) — The platform this app is shipped on.
* [vercel-php](https://github.com/vercel-community/php) — The PHP runtime for Vercel functions.

---

**Built to create. Designed to evolve.**

**ANWAR ACHILLES ©**
