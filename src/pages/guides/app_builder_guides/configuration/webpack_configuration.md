---
title: Webpack Configuration
description: >-
  Users can specify a Webpack configuration for an individual action, a set of
  actions, or an entire project.
keywords:
  - Webpack
  - Configuration
  - Developer Tooling
---

# Webpack Configuration

## Overview

App Builder bundles Runtime action code using Webpack. Users can specify Webpack configurations for individual actions, sets of actions, or entire projects. See [the Webpack site](https://webpack.js.org/configuration) for configuration options.

## Configuration file

When Runtime action code is bundled during `aio app build|run|deploy` the action directory is searched for a webpack config file named `*webpack-config.js`. The search is done first in the directory of the action being built, then the parent directory, up to the project root.

- To specify a Webpack configuration for a single action, place the `*webpack-config.js` file in the action folder.

- To specify a Webpack configuration for a set of actions, place the `*webpack-config.js` file in the parent directory of your action
  folders.

- To specify a Webpack configuration for an entire project, place the `*webpack-config.js` file in the root directory of your project.

### ES module syntax

App Builder does not currently support ES Module syntax. This file must be written in CommonJS, or failures will occur at build time. 

## Configuration types

App Builder supports exporting the following configuration types in `*webpack-config.js`:

### Objects

```javascript
module.exports = { 
  mode: 'development' 
}
```

### Functions that return objects

```javascript
module.exports = (env) => ({
  mode: 'development'
})
```

### Functions that return arrays of objects

```javascript
module.exports = (env) => ([
  {
    mode: 'development' 
  },
  {
    mode: 'production'
  }
])
```

### Asynchronous functions that return objects

```javascript
module.exports = async (env) => ({
  mode: 'development'
})
```

### Asynchronous functions that return arrays of objects

```javascript
module.exports = async (env) => ([
  {
    mode: 'development' 
  },
  {
    mode: 'production'
  }
])
```

### Arrays of objects

```javascript
module.exports = [
  {
    mode: 'development'
  },
  {
    mode: 'production'
  }
]
```

### Arrays of functions that return objects

```javascript
module.exports = [
  (env) => ({
    mode: 'development'
  }),
  (env) => ({
    mode: 'production'
  })
]
```

### Arrays of asynchronous functions that return objects

```javascript
module.exports = [
  async (env) => ({
    mode: 'development'
  }),
  async (env) => ({
    mode: 'production'
  })
]
```

## Configuration

### Overview

Assuming that `config` contains your Webpack configuration:

```javascript
{
  mode: config.mode || 'production', 
  optimization: {
    minimize: config.optimization.minimize || false
  },
  output: { 
    libraryTarget: config.output.libraryTarget || 'commonjs2',
    path: './dist/${actionPath}/${actionName}-temp/' // Cannot change 
    fileName: `${output.path}/index.js` // Cannot change 
  },
  target: 'node' // Cannot change
  entry: [
    '<path to the action>', 
    ...config.entry
  ],
  resolve: { 
    extensions: [
      '.js', 
      '.json', 
      ...config.extensions
    ],
    mainFields: [ 
      'main',
      ...config.mainFields
    ]
  },
  plugins: [
    new DefaultPlugin({
      WEBPACK_ACTION_BUILD: 'true', 
      `process.env.AIO_CLI_ENV`: ${cliEnv}
    }),
    ...config.plugins
  ]

  // ... All other Webpack configuration options are valid

}
```

### Base options

App Builder starts off with these Webpack configuration options; any additional values will be added on for these fields:

* `entry` always contains the path to the action; additional paths identified in your configuration file will be added on
* `resolve.extensions` always contains `.js` and `.json`; additional extensions identified in your configuration file will be added on.
* `resolve.mainFields` always contains `main`; additional main fields identified in your configuration file will be added on.

### Defaults

App Builder defaults to the following Webpack configuration options if they are not present in your configuration file:

* `output.libraryTarget` defaults to `commonjs2`
* `mode` defaults to `production`
* `optimization.minimize` defaults to `false`

### Immutable options

App Builder does not allow changing of these Webpack configuration options; if these fields are present in your configuration file, they will be ignored:

* `target` must be `node`; I/O Runtime supports only Node.js as a runtime
* `output.path`must be `./dist/${actionPath}/${actionName}-temp/`
* `output.filename` must be `${output.path}/index.js`
  * Note: If you are supplying multiple configurations, this will be true for the first configuration; subsequent configurations will be `${output.path}/index.[hashname].js`

## Environment variables

If you export a function, an asynchronous function, or an array of functions from your Webpack configuration file, App Builder will pass the environment to those functions. You can use this to make decisions about bundling based on the environment.

For example, if you export an environment variable called `FEATURE_FLAG_PIRATES_BOUNTY` before building your application, you can use it in your Webpack configuration file:

```javascript
module.exports = (env) => ({
  mode: env.FEATURE_FLAG_PIRATES_BOUNTY ? 'production' : 'development'
})
```

## Next steps

Return to [Configuration](configuration.md).

Return to [Guides Index](../../../guides/guides_index.md).
