# Configuración webpack para vue

```js
const path = require('path');
const webpack = require('webpack');

const bundleOutputDir = './wwwroot/dist';

module.exports = {
  entry: { main: './src/main.js' },
  output: {
    path: path.join(__dirname, bundleOutputDir),
    filename: '[name].js',
    publicPath: '/dist/',
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'vue-style-loader',
          'css-loader',
        ],
      },
      {
        test: /\.scss$/,
        use: [
          'vue-style-loader',
          'css-loader',
          'sass-loader',
        ],
      },
      {
        test: /\.sass$/,
        use: [
          'vue-style-loader',
          'css-loader',
          'sass-loader?indentedSyntax',
        ],
      },
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        // Esta parte permite agregar variables globales de estilo a toda la aplicación sin necesidad de importalas en cada componente
        options: {
          esModule: true,
          loaders: {
            sass: ['vue-style-loader',
              'css-loader',
              {
                loader: 'sass-loader',
                options: {
                  indentedSyntax: true,
                  data: '@import "index";',
                  includePaths: [
                    'src/assets/styles',
                  ],
                },
              },
            ],
            scss: ['vue-style-loader',
              'css-loader',
              {
                loader: 'sass-loader',
                options: {
                  data: '@import "index";',
                  includePaths: [
                    'src/assets/styles',
                  ],
                },
              },
            ],
          },
        },
      },
      {
        test: /\.js$/,
        loader: 'babel-loader',
        exclude: /node_modules/,
      },
      {
        test: /\.(png|jpg|gif|svg)$/,
        loader: 'file-loader',
        options: {
          name: '[name].[hash:7].[ext]',
          outputPath: 'img/',
        },
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        loader: 'url-loader',
        query: {
          limit: 10000,
          name: '[name].[hash:7].[ext]',
          outputPath: 'fonts/',
        },
      },
    ],
  },
  resolve: {
    alias: {
      vue$: 'vue/dist/vue.esm.js',
      '@': path.resolve(__dirname, 'src/'),
    },
    extensions: ['*', '.js', '.vue', '.json'],
  },
  devServer: {
    historyApiFallback: true,
    noInfo: true,
    overlay: true,
  },
  performance: {
    hints: false,
  },
  devtool: '#eval-source-map',
};

if (process.env.NODE_ENV === 'production') {
  module.exports.devtool = '#source-map';
  module.exports.plugins = (module.exports.plugins || []).concat([
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: '"production"',
      },
    }),
    new webpack.optimize.UglifyJsPlugin({
      sourceMap: true,
      compress: {
        warnings: false,
      },
    }),
    new webpack.LoaderOptionsPlugin({
      minimize: true,
    }),
  ]);
}
```