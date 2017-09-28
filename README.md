# Webpack best practices

## Multiple entrypoints
When a website consists of many pages with different functionality, it will be useful to have several entry points to load only the required js on the pages.

```js
// webpack.config.js
module.exports = {
 entry: {
   context: __dirname + '/frontend',
   home: '/home',
   about: '/about',
 },
 output: {
   path: __dirname + '/public',
   filename: '[name].js',
   library: '[name]',
 },
}
```

## No errors plugin
With NoErrorsPlugin webpack do not generate files when build is filed

```js
// webpack.config.js
module.exports = {
  //...
  plugins: [
    new webpack.NoErrorsPlugin(),
  ],
}
```

## CommonsChunkPlugin / common.js
Extracts common part from our endpoints into one file

```js
// webpack.config.js
module.exports = {
  //...
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'common',
      minChunk: 3, //Extracts code into common.js when build have same code in 3 files. Also can be a function.
      chunks: ['about', 'home'],
    }),

    //common-goods.js contains same part from shop and order modules
    new webpack.optimize.CommonsChunkPlugin({
      name: 'common-goods',
      chunks: ['shop', 'order'],
    }),
  ],
}
```

## Multi-compilation
TODO: Description

```js
module.exports = [{}, {}, {}]
```

## Dynamic require
TODO: Description

```js
module.exports = {
  output: {
    publicPath: '/',
  },

  require.ensure([], function(require) {
    let login = require('./login')

    login()
  }, 'auth') // => 1.auth.js

  require.ensure([], function(require) {
    let logout = require('./login')

    logout()
  }, 'auth') // => 1.auth.js
}
```

## Context replacement plugins
TODO: Description

```js
module.exports = {
  //...
  plugins: [
    new webpack.ContextReplacementPlugin(/node_modules\/moment\/locale/, /ru|en-gb/)
  ],
}
```

## Working with CDN's
TODO: Description

```js
module.exports = {
  //...
  externals: {
    lodash: '_'
  },
},
```

## Babel exclude
TODO: Description

```js
module.exports = {
  module: {
    loaders: [{
      test: /\.js$/,
      exclude: /\/node_modules\//,
      loader: 'babel'
    }],

    //noParse: /angular\/angulad.js/
    noParse: /\/node_modules\/(angular|jquery)/
  },
},
```

## Long caching
TODO: Description

```js
module.exports = {
  output: {
    path: __dirname + '/public/assets',
    publicPath: '/assets/',
    filename: '[name].[chunkhash].js',
    chunkFilename: '[id].js',
    library: '[name]'
  }
```
