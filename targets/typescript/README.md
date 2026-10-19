# TypeScript (invoice preview client)

Bob's bounded proof that the browser side of invoice preview can move from plain JavaScript to TypeScript: typed request and response models with runtime validation (`src/preview.ts`), browser wiring (`src/browser.ts`, `public/`) and a test runner (`src/preview.test.ts`). It calls only the preview route; it is one feature, not a full migration.

## Build and test

Needs Node.js (TypeScript 7.0.2 is installed by npm). Run from `targets/typescript`:

```powershell
cd targets/typescript
npm install
npm test        # tsc -p tsconfig.json, then node dist/preview.test.js
```

Verified here: `42 passed, 0 failed` on Node 26 with TypeScript 7.0.2.
