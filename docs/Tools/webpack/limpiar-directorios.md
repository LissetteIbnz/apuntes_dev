# WEBPACK: Limpiar directorios antes de construir

## Limpiando la carpeta /dist

Como habrás notado nuestra carpeta /dist se ha vuelto bastante desordenada. Webpack generará los archivos y los colocará en la carpeta /dist por usted, pero no realiza un seguimiento de qué archivos están realmente en uso en su proyecto.

En general, es una buena práctica limpiar la carpeta /dist antes de cada compilación, de modo que sólo se generarán los archivos usados.

Un plugin popular para administrar esto es el `clean-webpack-plugin`, así que vamos a instalarlo y configurarlo.

`npm install clean-webpack-plugin --save-dev`

*webpack.config.js*

```diff
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
+ const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
+     new CleanWebpackPlugin(['dist']),
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

Ahora ejecuta una `npm run build` e inspecciona la carpeta /dist. Si todo ha ido bien, ahora sólo debería ver los archivos generados a partir de la compilación y no más archivos antiguos.

## Opciones de clean-webpack-plugin

### Paths (Required)

An [array] of string paths to clean

```js
[
  'dist',         // removes 'dist' folder
  'build/*.*',    // removes all files in 'build' folder
  'web/*.js'      // removes all JavaScript files in 'web' folder
]
```

### Options and defaults (Optional)

```js
{
  // Absolute path to your webpack root folder (paths appended to this)
  // Default: root of your package
  root: __dirname,

  // Write logs to console.
  verbose: true,
  
  // Use boolean "true" to test/emulate delete. (will not remove files).
  // Default: false - remove files
  dry: false,           

  // If true, remove files on recompile. 
  // Default: false
  watch: false,

  // Instead of removing whole path recursively,
  // remove all path's content with exclusion of provided immediate children.
  // Good for not removing shared files from build directories.
  exclude: [ 'files', 'to', 'ignore' ],

  // allow the plugin to clean folders outside of the webpack root.
  // Default: false - don't allow clean folder outside of the webpack root
  allowExternal: false
  
  // perform clean just before files are emitted to the output dir
  // Default: false
  beforeEmit: false
}

```

## Ejemplo de la configuración Webpack

This is a modified version of WebPack's Plugin documentation that includes the Clean Plugin.

```js
const CleanWebpackPlugin = require('clean-webpack-plugin'); //installed via npm
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins
const path = require('path');
 
// the path(s) that should be cleaned
let pathsToClean = [
  'dist',
  'build'
]

// the clean options to use
let cleanOptions = {
  root:     '/full/webpack/root/path',
  exclude:  ['shared.js'],
  verbose:  true,
  dry:      false
}

// sample WebPack config
const webpackConfig = {
  entry: './path/to/my/entry/file.js',
  output: {
    filename: 'my-first-webpack.bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        loader: 'babel-loader'
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(pathsToClean, cleanOptions),
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
}

```