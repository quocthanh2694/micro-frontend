# Micro Frontends Application
Micro frontend advanced with ReactJS and Config CI CD plus AWS configuration - Config Amazon Web Services

### Important Versions Update / Boilerplate for `./ecomm-boilerplate/products`
In the upcoming lecture, we will be installing our dependencies. Some of the versions used in the course lectures have since broken.

Working versions can be installed by running the following instead:

`npm install webpack@5.68.0 webpack-cli@4.10.0 webpack-dev-server@4.7.4 faker@5.1.0 html-webpack-plugin@5.5.0`

An optional boilerplate for the project built in sections 1 to 3 can be found attached to this lecture as a zip file. If you choose to use the boilerplate, change into each directory (products, container, and cart) and then run `npm install`.

## Important Versions Update for `./ecomm-boilerplate/container`
In the upcoming lecture, we will be installing our dependencies for the container project. Similar to the products project, we will need to install slightly different versions.

Working versions can be installed by running the following instead:

`npm install webpack@5.68.0 webpack-cli@4.10.0 webpack-dev-server@4.7.4 html-webpack-plugin@5.5.0 nodemon`

## See `ModuleFederationPlugin` for export and implement module

### Singleton for share module:
```
    // ecomm-boilerplate\products\webpack.config.js
    new ModuleFederationPlugin({
      name: 'products',
      filename: 'remoteEntry.js',
      exposes: {
        './ProductsIndex': './src/index',
      },
      shared: {
        faker: {
            singleton: true,
        },
      },
    }),

```