# AGENTS.md

## What this is
OpportunityHub is an **Expo / React Native** app (single `App.js`, no backend, no database).
All state — bookmarks, submitted listings — is in-memory React state and resets on reload.

## Running it in the sandbox
```sh
docker compose -f docker-compose.base44.yml up -d
```
That runs Expo's **web** dev server (Metro) on container port 3000, mapped to host 3000 (the preview).
There is no API service and no database: no external credentials are required to run it.

## Non-obvious findings
- **Do not set `CI` for the `web` service.** Expo/Metro switches to CI mode when `CI` is set, which
  disables the file watcher and therefore live reload. A browser launch attempt from the container is
  harmless. This is the one setting worth remembering when editing the compose file.
- The project shipped with no web support. `react-dom`, `react-native-web` and `@expo/metro-runtime`
  were added to `package.json` (versions taken from `expo@54`'s `bundledNativeModules.json`) because
  Expo serves the browser preview through those. Removing them breaks the preview.
- Dependencies are installed into a named volume (`node_modules`) on container start, so `docker compose
  ... up -d` is enough after a `package.json` change — no image rebuild. The image (`node:22`) holds no app code.
- `Alert.alert` is a **no-op** on react-native-web (`Alert.alert() {}`), so on web the prototype's
  "Submitted" / "Missing details" / "Report" dialogs never appear. Expected, not a bug.
- Expo prints a `react-native@0.81.4 - expected version: 0.81.5` warning on start. Harmless here;
  web bundles and runs fine.

## How to verify it works
Append `?platform=web` is not needed; the root URL serves the app. After boot:
- `curl -s -o /dev/null -w '%{http_code}' http://127.0.0.1:3000/` → `200`
- In a browser: six sample listings render on Home, category chips and the search box filter them,
  a card opens its detail view, the bookmark toggle fills the Bookmarks tab, the Submit tab adds a
  `PENDING REVIEW` card on Home, and the Info tab shows Trust & Safety.
- Check for build errors in the Metro log: `docker compose -f docker-compose.base44.yml logs web`.
