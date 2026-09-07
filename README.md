# Dastaan-e-Kitab — GitHub + Netlify deployment

Orders now have a real shared backend (Netlify Blobs — built into every
Netlify site, no separate database account needed). Blobs live in their own
storage bucket tied to the *site*, completely separate from your deployed
files. That means: **however many times you delete and re-upload
`index.html` to add new books, every order ever placed stays exactly where
it was.**

## 1. Push this folder to GitHub

```bash
git init
git add .
git commit -m "Dastaan-e-Kitab bookstore"
```
Create a new empty repo on GitHub (github.com → New repository), then:
```bash
git remote add origin https://github.com/<your-username>/<repo-name>.git
git branch -M main
git push -u origin main
```

## 2. Connect it to Netlify

1. Go to https://app.netlify.com → **Add new site → Import an existing project**
2. Choose **GitHub**, authorize it, and pick this repository
3. Build settings: leave them as detected — this project needs no build
   step (publish directory `.`, functions directory `netlify/functions`,
   both already set in `netlify.toml`)
4. Click **Deploy**

That's it — no environment variables, no database setup. Netlify installs
`@netlify/blobs` automatically and the function just works.

## 3. Adding more books later

Same workflow as before — edit `index.html` (or use `add_book.sh`), then
replace the file on GitHub and push. Netlify redeploys automatically.
**Existing customer orders are untouched**, because they live in Netlify
Blobs, not inside `index.html`.

## 4. Checking orders

Open `https://<your-site>.netlify.app/#whitex` — the admin order log. The
note at the bottom tells you which storage tier is active:

- **"permanent shared cloud storage (Netlify Blobs)"** — you're live, every
  order from every customer/device shows up here, forever.
- **"previewing inside Claude"** — you're still inside Claude.ai's preview,
  not yet deployed.
- **"only being saved to this browser's local storage"** — the Netlify
  function isn't reachable (opened as a bare local file, offline, or not
  deployed yet).

You can also inspect/clear the raw data from the Netlify dashboard under
your site → **Blobs** → `dastaan-e-kitab-orders`.

## Notes

- **No login/auth on the admin page** — anyone who knows `#whitex` can see
  all orders. Fine for a small personal store; if you want it locked down,
  Netlify supports password-protecting a whole site (Site settings →
  Access control), or we can add real admin auth later.
- To test locally before deploying: install the Netlify CLI
  (`npm install -g netlify-cli`), then run `netlify dev` from this folder —
  it emulates both the static site and the function together, including
  Blobs storage.
