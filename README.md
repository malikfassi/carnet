# Carnet de cours

A static page (GitHub Pages) with no server and no data in the repo.

- **Sign-in**: Microsoft account (MSAL, SPA + PKCE), entirely in the browser.
- **Settings**: `config.json` in the user's OneDrive app folder (`Apps/<app name>/`), synced across devices; `localStorage` is only a display cache.
- **Preply lessons**: read from Outlook emails (Microsoft Graph, read-only). Nothing is stored; everything is recomputed on load.

> The repo is public: never commit `config.json`, the Excel files or the PDFs (see `.gitignore`).

## 1. Register the app in Microsoft Entra (once)

1. https://entra.microsoft.com → **App registrations** → **New registration**.
2. Name: `Carnet de cours` (this name appears on the sign-in screen and in OneDrive's `Apps/` folder).
3. **Supported account types**: *Accounts in any organizational directory and personal Microsoft accounts*.
4. **Redirect URI**: platform **Single-page application (SPA)**, URL `https://<user>.github.io/<repo>/`
   (and `http://localhost:5173/` to test locally).
5. **API permissions** → Microsoft Graph → *Delegated*: `User.Read`, `Mail.Read`, `Files.ReadWrite.AppFolder`. No admin consent needed for a personal account.
6. Copy the **Application (client) ID** into `env.js` → `CLIENT_ID`.

No client secret: it's a public application, and the Client ID is not a secret.

## 2. Publish on GitHub Pages

```bash
cd site
git init && git add . && git commit -m "Carnet de cours"
gh repo create carnet --public --source . --push
gh api repos/{owner}/carnet/pages -X POST -f "source[branch]=main" -f "source[path]=/"
```

The page is then available at `https://<user>.github.io/carnet/`.

## 3. On Zineb's side

1. **Outlook rule**: Settings → Mail → Rules → *From* contains `preply` → *Move to* a **Preply** folder.
   (Without this folder, the page searches the whole mailbox for emails from Preply.)
2. Open the URL, **Sign in with Microsoft**, accept the permissions.
3. First time: the page creates `config.json` in her OneDrive with the default settings, and she fills them in in Réglages.
4. Réglages → **Emails Preply** → *Check*: should list the latest Preply emails.

## Testing locally

With `CLIENT_ID` empty, the page runs in **local mode**: no account, settings in the browser only.

```bash
python3 -m http.server 5173 --directory site
```
