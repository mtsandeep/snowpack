## Recipes

Below are a collection of guides for using different web frameworks and build tools with Snowpack. If you'd like to contribute a new recipe, feel free to edit the docs to add your own.

### Supported Libraries

All of the following frameworks have been tested and guaranteed to work in Snowpack without issues. If you encounter an issue using any of the following, please file an issue.

- React
- Preact
- JSX
- HTM
- lit-html
- Vue (see below)
- Svelte (see below)
- Tailwind CSS (see below)
- Sass (see below)
- and many more!
- Literally... every library should work with Snowpack!

Some libraries use compile-to-JS file formats and do require a special build script or plugin. See the guide below for examples.

### Babel

Babel will automatically read plugins & presets from your local project `babel.config.*` config file, if one exists.

#### via plugin (Recommended)

```js
// snowpack.config.json
"plugins": ["@snowpack/plugin-babel"],
"scripts": {
  "build:js,jsx": "@snowpack/plugin-babel"
}
```

#### via @babel/cli

```js
// snowpack.config.json
// NOTE: Not recommended, Babel CLI is slower than the plugin on large sites.
"scripts": {
  "build:js,jsx": "babel --filename $FILE"
}
```

### Vue


```js
// snowpack.config.json
// Note: The plugin will add a default build script automatically
"plugins": ["@snowpack/plugin-vue"]
```

### Svelte

```js
// snowpack.config.json
// Note: The plugin will add a default build script automatically
"plugins": ["@snowpack/plugin-svelte"]
```


### PostCSS

```js
// snowpack.config.json
"scripts": {
  "build:css": "postcss"
}
```

The [`postcss-cli`](https://github.com/postcss/postcss-cli) package must be installed manually. You can configure PostCSS with a `postcss.config.js` file in your current working directory.

### CSS @import Support

The `@import` statements in CSS files [are not yet supported natively](https://github.com/pikapkg/snowpack/issues/389), meaning an `@import 'foo/bar.css'` (with a relative URL) will by default look for `foo/bar.css` in your app's `public/` directory only.

To allow relative `@import`s from the CSS files in your `src/` directory and to import CSS from other `node_modules`:
* Install PostCSS and add it to snowpack.config.json [as described above](#postcss)
* Install the [postcss-import](https://github.com/postcss/postcss-import) package
* Configure PostCSS to use the plugin, for example:
    ```js
    // postcss.config.js
    module.exports = {
      plugins: [
        // ...
        require('postcss-import')({path: ['resources/css']}),
        // ...
      ]
    ```

  If you're migrating an existing app to snowpack, note that `@import '~package/...'` (URL starting with a tilde) is a syntax specific to webpack. With `postcss-import` you have to remove the `~` from your `@import`s.

Alternatively [use `import 'path/to/css';` in your JS files without any configuration](#import-css).

### Tailwind CSS

```js
// postcss.config.js
// Taken from: https://tailwindcss.com/docs/installation#using-tailwind-with-postcss
module.exports = {
  plugins: [
    // ...
    require('tailwindcss'),
    require('autoprefixer'),
    // ...
  ]
}
```

Tailwind ships with first-class support for PostCSS. To use Tailwind in your Snowpack project, connect PostCSS ([see above](#postcss)) and add the recommended Tailwind PostCSS plugin to your snowpack configuration.

Follow the official [Tailwind CSS Docs](https://tailwindcss.com/docs/installation/#using-tailwind-with-postcss) for more info.

### Sass

```js
// snowpack.config.json
// Example: Build all src/css/*.scss files to public/css/*
"scripts": {
  "run:sass": "sass src/css:public/css --no-source-map",
  "run:sass::watch": "$1 --watch"
}

// You can configure this to match your preferred layout:
//
// import './App.css';
// "run:sass": "sass src:src --no-source-map",
//
// import 'public/css/App.css';
// "run:sass": "sass src/css:public/css --no-source-map",
// (Note: Assumes mounted public/ directory ala Create Snowpack App)
```

[Sass](https://www.sass-lang.com/) is a stylesheet language that’s compiled to CSS. It allows you to use variables, nested rules, mixins, functions, and more, all with a fully CSS-compatible syntax. Sass helps keep large stylesheets well-organized and makes it easy to share design within and across projects.

[Check out the official Sass CLI documentation](https://sass-lang.com/documentation/cli/dart-sass) for a list of all available arguments. You can also use the [node-sass](https://www.npmjs.com/package/node-sass) CLI if you prefer to install Sass from npm.

**Note:** Sass should be run as a "run:" script (see example above) to take advantage of the Sass CLI's partial handling. A ``"build:scss"` script would build each file individually as its served, but couldn't handle Sass partials via `@use` due to the fact that Sass bundles these into the importer file CSS.

To use Sass + PostCSS, check out [this guide](https://zellwk.com/blog/eleventy-snowpack-sass-postcss/).

### Workbox

The [Workbox CLI](https://developers.google.com/web/tools/workbox/modules/workbox-cli) integrates well with Snowpack. Run the wizard to bootstrap your first configuration file, and then run `workbox generateSW` to generate your service worker.

Remember that Workbox expects to be run every time you deploy, as a part of a production "build" process (similar to how Snowpack's [`--optimize`](#production-optimization) flag works). If you don't have one yet, create package.json [`"deploy"` and/or `"build"` scripts](https://michael-kuehnel.de/tooling/2018/03/22/helpers-and-tips-for-npm-run-scripts.html) to automate your production build process.

### Leaving Snowpack

Snowpack is designed for zero lock-in. If you ever feel the need to add a traditional application bundler to your stack (for whatever reason!) you can do so in seconds.

Any application built with Snowpack should Just Work™️ when passed through Webpack/Rollup/Parcel. If you are already importing packages by name in your source code (ex: `import React from 'react'`) then you should be able to migrate to any popular bundler without issue.

If you are importing packages by full URL (ex: `import React from '/web_modules/react.js'`), then a simple Find & Replace should help you re-write them to the plain package name imports that most bundlers expect.
