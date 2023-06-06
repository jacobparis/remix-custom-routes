# Remix Route Extensions Convention

This convention is good for domain driven design, feature folders, or any similar architecture that values colocation of related files.

- Instead of a `/routes` directory, routes can be defined in any directory (within `/app` but not at the top level).
- Any file with the extension `.route.tsx`, `.route.ts`, `.route.jsx`, or `.route.js` will be treated as a route.
- All other files are ignored, so you can safely put non-route files next to your routes. Components, hooks, images, etc.
- URLs are determined by the filename, just like [Remix's v2 route convention](https://remix.run/docs/en/main/file-conventions/route-files-v2).
- Folders do not participate in routing, so you can organize your routes however you want. As long as the file has `.route` in the name, Remix will find it and turn it into a route.

```ts
//remix-config.js
const { routeExtensions } = require("remix-custom-routes")

module.exports = {
  ignoredRouteFiles: ["routes/**.*"], // ignore the default route files
  async routes() {
    const appDirectory = path.join(__dirname, "app")

    return routeExtensions(appDirectory)
  },
}
```

## Implementation

The `routeExtension` export is a shortcut for the following code. Feel free to use the unbundled functions if you would like more control over the implementation.

```ts
//remix-config.js
const glob = require("glob")
const {
  getRouteIds,
  getRouteManifest,
  ensureRootRouteExists,
} = require("remix-custom-routes")

module.exports = {
  async routes() {
    const appDirectory = path.join(__dirname, "app")
    ensureRootRouteExists(appDirectory)

    // array of paths to files
    const files = glob.sync("**/*.route.{js,jsx,ts,tsx,md,mdx}", {
      cwd: appDirectory,
    })

    // array of tuples [routeId, filePath]
    const routeIds = getRouteIds(files, {
      suffix: ".route",
    })

    // Remix manifest object
    return getRouteManifest(routeIds)
  },
}
```