# failure to run sveltekit with vite3 in a TypeScript monorepo

Run the following to enable corepack and install all dependencies (using `pnpm`).

```bash
npm run bootstrap
```

At this point you'll notice the `dist` folders is created in `packages/simple`. Take a look at `packages/simple/dist/index.esm.js`. It is a symlink which links to this source file `packages/simple/src/index.ts` during development. It is replaced with the bundled output during a build.

## Expected

There is a svelte app built with vite in the `/svelte` folder. It imports from the `simple` package in `svelte/src/App.svelte`. Running the following command works fine.

```bash
cd svelte
pnpm dev
```

You will be able to access the URL without error http://localhost:5173/.

## Issue

The issue arises when using the same import in a sveltekit setup.

I've imported the `simple` package in `sveltekit/src/routes/index.svelte`. Running the following:

```bash
cd sveltekit
pnpm dev
```

Results in the following error when navigating to the URL: http://localhost:5173/

```bash
Unknown file extension ".ts" for /path/to/root/vite3-unknown-file-extension/packages/simple/src/index.ts
TypeError [ERR_UNKNOWN_FILE_EXTENSION]: Unknown file extension ".ts" for /path/to/root/vite3-unknown-file-extension/packages/simple/src/index.ts
    at new NodeError (node:internal/errors:377:5)
    at Object.getFileProtocolModuleFormat [as file:] (node:internal/modules/esm/get_format:80:11)
    at defaultGetFormat (node:internal/modules/esm/get_format:122:38)
    at defaultLoad (node:internal/modules/esm/load:21:20)
    at ESMLoader.load (node:internal/modules/esm/loader:431:26)
    at ESMLoader.moduleProvider (node:internal/modules/esm/loader:350:22)
    at new ModuleJob (node:internal/modules/esm/module_job:66:26)
    at #createModuleJob (node:internal/modules/esm/loader:369:17)
    at ESMLoader.getModuleJob (node:internal/modules/esm/loader:328:34)
    at async Promise.all (index 0)
```

## Workaround

The current solution is to downgrade the vite version to `vite@2.9.14` and `@sveltejs/kit@1.0.0-next.372`. You can test this out by running the following:

```bash
cd sveltekit-previous
pnpm dev
```

Navigating to http://localhost:3000/ will work.
