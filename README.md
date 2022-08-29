# Micro Frontends Application
Micro frontend advanced with ReactJS and Config CI CD plus AWS configuration - Config Amazon Web Services

# Part 1: Bacis with js project - Micro js project
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


# Part 2: Advanced with mordent framwork & lib (React, Vue, Angular...) - Micro React & vue project

# AWS
### Bucket console: bucket setup
1. Go to S3
2. Create new bucket
3. Setting bucket -> Properties -> Static website hosting -> Edit -> 
   1. Enable
   2. Host a static website
   3. Index document: index.html
   4. Error document: index.html
   5. Save changes
4. Setting bucket -> Permission:
   1. Block public access (bucket settings): Uncheck: Block all public access
   2. Click Save
5. Setting bucket -> Permission:
   1. Bucket policy: Edit: Policy generator(new browser)
      1. Slect Type of Policy: S3 bucket policy
      2. Principle: *
      3. Actions: GetObject
      4. Amazon Resource name (ARN): [Bucket ARN]/* (copy from `Edit bucket policy` and then add `\*`)
      5. Click on Add statement
      6. Click Generate Policy
      7. Copy all the text genrated
      8. Paste in to `Edit bucket policy` page in sectiono Policy Text editor.
   2. Click Save changes.
   
- Note: 60. Minor Changes in AWS CloudFront UI
### Authorize bucket
1. Open Cloudfront -> Create Distribution -> Web -> Get Started
   1. Origin Domain Name: Find the name of bucket that have just created above
   2. Default cache behavior settings: 
      1. Viewer  protocol policy: Redirect HTTP to HTTPS
   3. Distribution settings: 
      1. SSL certificate: (required when you have another domain name)
   4. Click Create distribution
2. After Distribution was created -> click and go to detail
   1. General Tab: Click Edit 
      1. Default root object: /container/latest/index.html
      2. Click Yest Edit
   2. Error pages Tab: Click Create custom error response
      1. HTTP error code: 403 forbidden
      2. Custom error response: yes
      3. Response page path: /container/latest/index.html
      4. HTTP response code: 200: OK
      5. Click: Create
   3. General Tab: 
      1. Domain name: will show the link you can access your web app (try it)
- Note: 63. Reminder on the AWS_DEFAULT_REGION
  
### Generate public and private key and rebuild and test web
1. AWS:
   1. Open IAM: CLick User in left sidebar
   2. Click Add user
      1. User name: yourname-git-action (depend on you)
      2. Access type: Programatic access
      3. Click "Next: permission"
      4. Click Attach existing policies directly
      5. Find policies: s3 -> check in AmazonS3FullAccess
      6. Fined policies: cloudfront -> check in CloudfrontFullAccess
      7. Click "Next: Tags"
      8. Click "Next: Review"
      9. Click Create user.
      10. After created you will see `Access key ID` and `secret access key`. Copy it (paste in github).
2. Github: 
   1. Click on Tab Settings: 
      1. Click on Secrets tab -> Create new
         1. Name: AWS_ACCESS_KEY_ID (the name you use as a variable in yml file)
         2. Value:  Pass the `Access key ID` from aws
         3. CLick Add.
      2. Click new secret again for `AWS_SECRET_ACCESS_KEY` and `secret access key` in aws.
      3. Click new secret again for `AWS_S3_BUCKET_NAME` and value is `exac name of  s3 bucket`
3. Done: Go back to tab actions on github and click re-run to test deploy -> when status is success
4. Go back to `cloudfront general tab in aws` click on the `Domain name` to test -> You will see blank (cause by wrong build js file path).
5. Update `webpack.prod.js`: add public path. -> Rebuild and Redeploy -> test again -> nothing change cause by cache.
6. Go to bucket detail again -> Click refesh the object list (file list)
   1. Go to `container/latest/index.html` -> Click object url (link to open this file) -> you will see index.html file was updated
7. Update index.html whenever you change this file: Go to `cloudfront` -> click tab `Invalidations` 
   1. Create invalidation: 
      1. Object path: `/container/latest/index.html` -> Click Invalidate => Your index.html will auto update after change.
      2. Open browser and test your web again -> you'll see new index.html change.
- Note: 69. AWS Region with Automatic Invalidation
8. Update automatically clear cache and update app in aws: Go to yml and add linne: `      - run: aws cloudfront create-invalidation --distribution-id ${{ secrets.AWS_DISTRIBUTION_ID }} --paths "/container/latest/index.html"`
9. Go to github and add new secret: `AWS_DISTRIBUTION_ID` with value in the `cloudfront distribution > [ID]`
10. Push to github and wait for deploy and test again. You can see invalidation detail on cloudfront -> invalidation

### Config Dynamic production domain after build from github to call in `packages\container\config\webpack.prod.js`
1. Find Cloudfront Distribution > [Your CDN name]
2. General -> Domain Name -> Copy it.
3. Add `PRODUCTION_DOMAIN` to secrets of GitHub (make sure add https to the link: htts://....)

### Summary: 77. AWS Setup and Configuration Cheetsheet.