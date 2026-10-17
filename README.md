# OpportunityHub — Expo starter

Mobile-first React Native starter with six clearly labeled sample listings, category filters, search, detail pages, bookmark toggles, submission form, and safety information.

## Run
1. Install Node.js LTS.
2. Run `npm install` in this folder.
3. Run `npx expo start` and scan the QR code with Expo Go.

## Run in the Base44 sandbox (browser preview)

The preview is a browser, so here the app is served through Expo's **web**
target rather than Expo Go:

```bash
docker compose -f docker-compose.base44.yml up -d --build
```

This runs `expo start --web` on <http://localhost:3000> in a `node:22` container,
using the bind-mounted source (no image rebuild for code edits). The web target
needs `react-dom`, `react-native-web` and `@expo/metro-runtime`, which are now in
`package.json`. No credentials, database or external service are required.

Notes: do not set `CI=1` for this service — Expo then starts Metro with reloads
disabled. Metro runs in watch mode with `CHOKIDAR_USEPOLLING=1` so edits on the
bind mount are picked up. On web, React Native's `Alert.alert()` is a no-op
(react-native-web stubs it), so the prototype's message dialogs appear only in
Expo Go.

## Important limitations
This is a UI prototype. Bookmarks and submissions are in-memory and reset after restart. Reporting is a demonstration alert. The `Pending review` label is not server-side moderation or access control. Before public launch, add authentication, a database, durable bookmarks, URL validation, real reporting, and secure administrator-only approval. Do not publish user submissions until this exists. Demo listings are not real offers. No payment or Pi wallet integration is implemented.
