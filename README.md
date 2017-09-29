# Webpack best practices

## Using Multiple entrypoints
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

## Using No errors plugin
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

## Using CommonsChunkPlugin / common.js
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

## Using Multi-compilation
Runs a few builds in one time. Multi-compilation is useful when your have diffrent builds. For example: with diffrent locales or for diffrent browsers.

```js
module.exports = [{
  //...
}, 
{
  //...
}, 
{
  //...
}]
```

## Using Dynamic require
Require modules in runtime. When we actualy need it.

```js
//app.js
require.ensure([], function(require) {
  let login = require('./login')

  login()
}, 'auth') // => 1.auth.js

require.ensure([], function(require) {
  let logout = require('./login')

  logout()
}, 'auth') // => 1.auth.js
  
//webpack.config.js  
module.exports = {
  //...
  output: {
    publicPath: '/',
  },
}
```

## Using Context replacement plugins
Decrease build size by excluding useless modules of third party library. For example: exclude useless locales of momemnt.js

```js
module.exports = {
  //...
  plugins: [
    new webpack.ContextReplacementPlugin(/node_modules\/moment\/locale/, /ru|en-gb/)
  ],
}
```

## Working with CDN's
Integrates CDN library in webpack build. Add possibility to require CDN library.

```js
module.exports = {
  //...
  externals: {
    lodash: '_'
  },
},
```

## Using ProvidePlugin
Decreasing build size and simplify library importing.

```js
module.exports = {
  //...
  plugins: [
   new webpack.ProvidePlugin({
     pluck: 'lodash/collection/pluck',
   }),
  ],
}
```

## Using Babel exclude
Excluding third party libraries from babel processing.

```js
module.exports = {
  module: {
    loaders: [{
      test: /\.js$/,
      exclude: /\/node_modules\//,
      loader: 'babel'
    }],
    noParse: /\/node_modules\/(angular|jquery)/
  },
},
```

## Using Long caching
Adds hash to filename.

```js
module.exports = {
  output: {
    path: __dirname + '/public/assets',
    publicPath: '/assets/',
    filename: '[name].[chunkhash].js', //'chunkhash' means hash for this file. 'hash' means hash for all build
    chunkFilename: '[id].js',
    library: '[name]'
  }
```

## Using Hot module replacement
TODO: Description

```js
 //TODO: add js 
```
