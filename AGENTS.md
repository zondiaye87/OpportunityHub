# AGENTS.md — running OpportunityHub in the Base44 sandbox

Non-obvious notes only; see `README.md` for what the app is.

## What the app is
A single-file Expo (React Native, SDK 54) prototype: `App.js`, entry
`node_modules/expo/AppEntry.js`. There is no navigation library, no backend, no
database and no external API — everything is in-memory React state.

## How it runs here
`docker-compose.base44.yml` starts one `web` service from the **web** target of
Expo's Metro dev server (`expo start --web`). The preview iframe needs a page on
host port 3000, and Expo Go / native builds can't be shown in a browser, so the
web target is the only way to see this app in the preview.

- Compose must run the service with `CI=1`: Expo CLI would otherwise try to
  prompt / open a browser.
- The web target needs `react-dom`, `react-native-web` and `@expo/metro-runtime`
  in addition to the native deps that shipped with the starter. They were added
  with `npx expo install`; keep versions SDK-pinned that way.
- `node_modules` lives in the `web_node_modules` volume (not the repo), because
  `npm install` runs at container start. If `package.json` changes, restart the
  service so the install re-runs.
- `CHOKIDAR_USEPOLLING=1` is set: bind mounts in the sandbox don't reliably
  deliver inotify events, and Metro's watcher would miss edits without polling.

## Verify it works
```bash
docker compose -f docker-compose.base44.yml up -d --build
docker compose -f docker-compose.base44.yml ps          # web should be healthy
curl -s http://localhost:3000/ | head                 # Expo serves the HTML shell
```
The first browser request triggers the Metro web bundle compile, which can take
~30-60s; a slow first paint is build time, not a failure.

## Known web-target limitation (pre-existing, not a setup problem)
`Alert.alert()` from `react-native` is a **no-op** on web (react-native-web
stubs it out). The submit-form validation/success messages, the "demo listing"
notice on "Open official link", and the "Report suspicious listing" button
therefore show no dialog in the preview — they work in Expo Go on a device.
App logic itself still runs (submissions are added in memory, tabs switch).
