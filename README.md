# esmodule-server

esmodule-server is a simple, zero-configuration ES module supported HTTP server. It is powerful enough for visit ES module directly by modern browser. Fully support Typescript and React.

## Features

- Based on [Deno](https://deno.land), keep same code style with it.
- Fully support Typescript.
- Fully support React syntax, can visit `jsx` or `tsx` by modern browser directly.
- Natively support React App and SSR.
- Production and Dev mode support by parameters(`?dev`, `?production`).

## Running

### Run with docker

```
# ~/Downloads/static is local static directory, 8888 is local port

docker run -p 8888:8000 -v ~/Downloads/static:/workspaces --restart unless-stopped -d --name esmodule-server hayond/esmodule-server
```

### Run with deno

```
deno run --allow-read --allow-net --allow-write https://deno.land/x/esmoduleserver/mod.js
```

## Examples

### simple server

create `index.html` and `index.jsx`

```HTML
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, user-scalable=no, shrink-to-fit=no"
    />
    <title>index</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="./index.jsx"></script>
  </body>
</html>
```
```javascript
import React from "https://esm.sh/react";
import { createRoot } from "https://esm.sh/react-dom/client";

const root = createRoot(document.querySelector("#root"));
root.render(<div>{content}</div>, { content: "Hello World!" });
```

### react app
only need create `index.jsx`
```javascript
import React from "https://esm.sh/react";

function App({ content }) {
  return <div>{content}</div>;
}

export const title = "esmodule-server";

export default function main({ preloadedState }) {
  return React.createElement(App, { content: "Hello World!" });
}
```

### react ssr app
implement export function `getPreloadedState` can open SSR, function `getServerReady` is useful server check whether data is ready, maximum try three times when return false.
```javascript
import React from "https://esm.sh/react";

function App({ content }) {
  return <div>{content}</div>;
}

export const title = "esmodule-server";

const store = {
  content: "Hello World!",
  isLoaded: true,
};

export function getPreloadedState() {
  return { ...store };
}

export function getServerReady() {
  return store.isLoaded;
}

export default function main({ preloadedState = {} }) {
  Object.assign(store, preloadedState);
  return React.createElement(App, { content: store.content });
}
```

### production and dev mode
- `?dev` add dev parameter to all link dependences, check[esm.sh](https://esm.sh/), for example `http://localhost:8000/example/react-ssr-app/index.html?dev`
- `?production` add bundle parameter to all link dependences, check[esm.sh](https://esm.sh/), in the meantime, bundle all the local script and minify them.

## uncoming feature
- set production mode as default by commandline args.
- custom server port by commandline args.
- ...
