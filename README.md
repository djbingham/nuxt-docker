# Nuxt Docker
This is a test repository for debugging issues running Nuxt in a Docker container.

## Dependencies
The only dependency of this project is Docker. The scripts provided will run Node and NPM commands inside standalone Docker containers.

## Usage
To run Nuxt dev mode in a standalone container, clone this repository and run:
```
script/prepare
script/dev
```

This will install dependencies then start a Nuxt dev server on port 80 in a Docker container. The `script/dev` script attaches to the started container, showing the log output. If you `ctrl + c` out of this, the container will be stopped and removed to avoid clogging up your system with old containers.


## HMR Issue
Currently, the hot module reloading (HMR) feature is not working correctly. To reproduce the issues, follow the steps below:

1. Install dependencies by running `script/prepare`.
2. Start the server with `script/dev` and wait for initial build to complete. The container log shows:
```
OPEN http://localhost:80
```

3. Go to `localhost` in a web browser. The page loads, showing the heading "Hello World!".
4. Edit `app/pages/index.vue`, removing the "!" to leave the heading as "Hello World". The container logs show compiling progress, then:
```
DONE  Compiled successfully in -10560ms
```

The browser automatically shows the updates page, as expected. However, *this is where weird things start to happen:*

5. Once the browser has updated, the container logs show an error below the previous "Compiled successfully" message:
```
  nuxt:render Rendering url / +32s
{ Error: Module build failed: Error: ENOENT: no such file or directory, open '/srv/app/pages/index.vue'
    at Object.44 (pages/index.js:8:7)
    at __webpack_require__ (webpack:/webpack/bootstrap 55191ef2777bf995a655:25:0)
    at process._tickCallback (internal/process/next_tick.js:68:7) statusCode: 500, name: 'NuxtServerError' }
  nuxt:render Rendering url /HNAP1/ +1s
{ statusCode: 404,
  path: '/HNAP1/',
  message: 'This page could not be found' }
```
6. Refresh the browser. The page is replaced with an error message:
```
render function or template not defined in component: anonymous
```
The container logs show:
```
  nuxt:render Rendering url / +6m
  nuxt:render Data fetching /: 0ms +0ms
{ Error: render function or template not defined in component: anonymous
    at normalizeRender (/srv/app/node_modules/vue-server-renderer/build.js:7407:13)
    at renderComponentInner (/srv/app/node_modules/vue-server-renderer/build.js:7531:3)
    at renderComponent (/srv/app/node_modules/vue-server-renderer/build.js:7502:5)
    at renderNode (/srv/app/node_modules/vue-server-renderer/build.js:7418:5)
    at renderComponentInner (/srv/app/node_modules/vue-server-renderer/build.js:7538:3)
    at renderComponent (/srv/app/node_modules/vue-server-renderer/build.js:7502:5)
    at renderNode (/srv/app/node_modules/vue-server-renderer/build.js:7418:5)
    at renderComponentInner (/srv/app/node_modules/vue-server-renderer/build.js:7538:3)
    at renderComponent (/srv/app/node_modules/vue-server-renderer/build.js:7502:5)
    at renderNode (/srv/app/node_modules/vue-server-renderer/build.js:7418:5)
    at renderComponentInner (/srv/app/node_modules/vue-server-renderer/build.js:7538:3)
    at renderComponent (/srv/app/node_modules/vue-server-renderer/build.js:7502:5)
    at RenderContext.renderNode (/srv/app/node_modules/vue-server-renderer/build.js:7418:5)
    at RenderContext.next (/srv/app/node_modules/vue-server-renderer/build.js:2436:14)
    at cachedWrite (/srv/app/node_modules/vue-server-renderer/build.js:2295:9)
    at renderElement (/srv/app/node_modules/vue-server-renderer/build.js:7656:5) statusCode: 500, name: 'NuxtServerError' }
```

7. Edit `app/pages/index.vue`, adding an "s" to make the heading "Hello worlds". The container logs show an error:
```
 ERROR  Failed to compile with 1 errors                                                                                                                          12:09:39 PM

 error  in ./pages/index.vue

Module build failed: Error: ENOENT: no such file or directory, open '/srv/app/pages/index.vue'

 @ ./.nuxt/router.js 8:9-75
 @ ./.nuxt/index.js
 @ ./.nuxt/client.js
 @ multi webpack-hot-middleware/client?name=client&reload=true&timeout=30000&path=/__webpack_hmr ./.nuxt/client.js
```

8. Refresh the browser. The updated page is displayed with an overlay showing the build error:
```
ERROR in ./pages/index.vue
Module build failed: Error: ENOENT: no such file or directory, open '/srv/app/pages/index.vue'
```
