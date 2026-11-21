# Conversion test report

## Performed
- ZIP archive was opened and its file listing inspected before conversion.
- Reviewed Android manifest, Kotlin app entry, model/catalog definitions, student registration repository, admin repository, student portal, admin tabs, security helper, PDF generator, and Firestore rules.
- JavaScript/JSX syntax was transpiled with the globally installed TypeScript compiler in JSX mode; **0 syntax diagnostics**.
- Transpiled JavaScript passed `node --check`.
- `src/firebase.js` passed `node --check`.
- CSS parsed successfully with PostCSS.
- `package.json`, `firebase.json`, and `.firebaserc.example` parsed as JSON.
- Required static assets and app entry files were checked for existence.
- Final ZIP was created and its CRC/content integrity was checked with Python `zipfile.testzip()`.

## Not completed (environment limitation)
- `npm install` was attempted, but this environment could not resolve `registry.npmjs.org` (DNS `EAI_AGAIN`), and dependency installation timed out.
- Consequently, **Vite production build and browser/runtime interaction tests were not executed**. `npm run build` was attempted and failed with `vite: not found` because dependencies could not be installed.
- Firebase Auth/Firestore, Google OAuth popup, QR camera, PDF generation, and deployment were not tested against a live Firebase project. They require the owner's Firebase configuration and HTTPS/localhost permissions.

## Conversion notes
- Android-native functionality was reimplemented using browser equivalents rather than simply renamed or extracted.
- Firestore rules from the Android source are retained, with the bootstrap owner email replaced by a placeholder to avoid shipping the original account address. Update the placeholder before deploying.
- Android's original 14-page PDF package is approximated in web PDF output; layout is not pixel-identical.
