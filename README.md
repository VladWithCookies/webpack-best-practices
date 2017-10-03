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

## Using CommonsChunkPlugin
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

It's also good practice to extract third-party libraries, such as ```lodash``` or ```react```, to a separate vendor chunk as they are less likely to change than our local source code. This step will allow clients to request even less from the server to stay up to date. This can be done by using a combination of a new entry point along with another CommonsChunkPlugin instance.

```js
var path = require('path');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  entry: {
    main: './src/index.js',
    vendor: [
      'lodash'
    ]
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      title: 'Caching'
    }),
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor'
    }),
    new webpack.optimize.CommonsChunkPlugin({
      name: 'runtime'
    })
  ],
  output: {
    filename: '[name].[chunkhash].js',
    path: path.resolve(__dirname, 'dist')
  }
};
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
The ProvidePlugin makes a package available as a variable in every module compiled through webpack. If webpack sees that variable used, it will include the given package in the final bundle.

```js
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
    },
    plugins: [
      new webpack.ProvidePlugin({
        lodash: 'lodash'
      })
    ]
  };
```

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

## Using HtmlWebpackPlugin
If we changed the name of one of our entry points, or added a new one then our generated bundles would be renamed on a build, but our index.html file would still reference the old names. HtmlWebpackPlugin can fix that by replacing our index.html file with a newly generated one.

```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
      new HtmlWebpackPlugin({
       title: 'Output Management'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

## Using CleanWebpackPlugin
In general it's good practice to clean the ```/dist``` folder before each build, so that only used files will be generated.

```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Output Management'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  }
```

## Using UglifyJSPlugin
Minifier that supports dead code removal 

```js
const path = require('path');
  const UglifyJSPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
  },
  plugins: [
    new UglifyJSPlugin()
  ]
};
```
