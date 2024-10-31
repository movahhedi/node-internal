# `node-internal`

An array of Node's internal module names.

It can be used to externalize Node's internal modules when bundling with Vite, Webpack, Browserify, etc.

## Install

Install it via [npm](https://www.npmjs.com/package/node-internal):
```sh
npm install -D node-internal
```
Or via [yarn](https://www.npmjs.com/package/node-internal):
```sh
yarn add -D node-internal
```
Or via [pnpm](https://www.npmjs.com/package/node-internal):
```sh
pnpm add -D node-internal
```

## Usage

Example use with Vite:
```ts
// vite.config.ts
import path from "path";
import { nodeInternalModules } from "node-internal";

import { defineConfig } from "vite";

const shouldBundleDependencies = true;

export default defineConfig({
		build: {
			rollupOptions: {
				external: (id) => {
					// There was an import named `"string_decoder/"` with a trailing slash somewhere.
					if (id.endsWith("/") || id.endsWith("\\")) {
						return false;
					}

					if (id.startsWith(".")) return false;
					if (path.isAbsolute(id)) return false;

					if (
						id.startsWith("node:") ||
						nodeInternalModules.find(
							(nodeInternalName) =>
								id === nodeInternalName ||
								id.startsWith(nodeInternalName + "/"),
						)
					)
						return true;
					// if (nodeInternalModules.includes(id)) return true;

					return shouldBundleDependencies ? false : true;
				},
			},
			commonjsOptions: {
				include: /node_modules/,
			},
		},
	});
```

