# Webpack best practices

## Using multiple entrypoints
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

## Using NoErrorsPlugin
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

## Using CommonsChunkPlugin/common.js
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

## Using multi-compilation
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

## Using dynamic require
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

## Using ContextReplacementPlugin
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

## Using compile-to-JS languages
Here's how you can teach webpack to load CoffeeScript and Facebook JSX+ES6 support (you must npm install babel-loader coffee-loader):

```js
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'       
  },
  module: {
    loaders: [
      { test: /\.coffee$/, loader: 'coffee-loader' },
      {
        test: /\.js$/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      }
    ]
  }
};
```

To enable requiring files without specifying the extension, you must add a resolve.extensions parameter specifying which files webpack searches for:

```js
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'       
  },
  module: {
    loaders: [
      { test: /\.coffee$/, loader: 'coffee-loader' },
      {
        test: /\.js$/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      }
    ]
  },
  resolve: {
    // you can now require('file') instead of require('file.coffee')
    extensions: ['', '.js', '.json', '.coffee'] 
  }
};
```

## Using babel exclude
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

## Using long caching
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

## Using hot module replacement
```js
module.exports = {
  entry: {
    main: ['webpack-dev-server/client', 'webpack-dev-server/hot/dev-server', './main']
  }
  
  //...
  
  devServer: {
    contentBase: __dirname + '/backend',
    hot: true
  }
}
```
Using with ExtractTextPlugin

```js
module.exports = {
  //...
  plugins: {
   new ExtractTextPlugin('[name].css', {allChunks: true, disable: process.env.NODE_ENV=='development'})
  }
}
```
