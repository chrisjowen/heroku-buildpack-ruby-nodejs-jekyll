Heroku buildpack: Jekyll with precompilation
============================================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for Jekyll Apps uses Ruby and Node.js ( with grunt).
Check out [NPM](http://npmjs.org/), [GruntJS](http://gruntjs.com/) and [SCons](http://www.scons.org/).

What does it do?
----------------

- it will detect a ruby application following the default [Ruby Buildpack](https://github.com/heroku/heroku-buildpack-ruby)
- it will detect your app includes Node.js if it has the file `package.json` in the root.  It will use NPM to install your dependencies, and vendors a version of the Node.js runtime into your slug.  The `node_modules` directory will be cached between builds to allow for faster NPM install time.
- For [Jekyll](http://jekyllrb.com/), you no longer need to pre-build the site or commit the _site build directory to your repo. This simplifies the deployment process and keeps the repo clean. 

Why do I care?
----------------

While Jekyll is cool all by itself, it is even cooler when you can use all the power and benefits of Ruby, Node, and Grunt to your hearts desire. Precompilation for coffeescript? Check. Ability to manage static resources? _Checkmate_. 

Jekyll Auth
-----------
Do you need to setup oAuth support, and have a github organization? Cool, check out [Jekyll Auth](https://github.com/benbalter/jekyll-auth)

Node.js and npm versions
------------------------

Specify the versions of Node.js and npm your application requires using `package.json`

    {
      "name": "myapp",
      "version": "0.0.1",
      "engines": {
        "node": ">=0.4.7 <0.7.0",
        "npm": ">=1.0.0"
      }
    }

To list the available versions of Node.js and npm, see these manifests:

http://heroku-buildpack-nodejs.s3.amazonaws.com/manifest.nodejs
http://heroku-buildpack-nodejs.s3.amazonaws.com/manifest.npm

Grunt Support
--------------
Supported Grunt versions: 0.3 and 0.4.
See the Grunt [migration guide](https://github.com/gruntjs/grunt/wiki/Upgrading-from-0.3-to-0.4) if you are upgrading from 0.3.

This is a fork of [Heroku's official Node.js buildpack](https://github.com/heroku/heroku-buildpack-nodejs) with added [Grunt](http://gruntjs.com/) support.
Using this buildpack you do not need to commit the results of your Grunt tasks (e.g. minification and concatination of files), keeping your repository clean.

After all the default Node.js and npm build tasks have finished, the buildpack checks if a Gruntfile (`Gruntfile.js`, `Gruntfile.coffee`or `grunt.js`) exists and executes the `heroku` task by running `grunt heroku`. For details about grunt and how to define tasks, check out the [offical documentation](http://gruntjs.com/getting-started). You must add grunt to the npm dependencies in your `package.json` file.
If no Gruntfile exists, the buildpacks simply skips the grunt step and executes like the standard Node.js buildpack.

### Usage

Create a new app with this buildpack:

    heroku create myapp --buildpack https://github.com/mbuchetics/heroku-buildpack-nodejs-grunt.git

Or add this buildpack to your current app:

    heroku config:add BUILDPACK_URL=https://github.com/mbuchetics/heroku-buildpack-nodejs-grunt.git

Add the sneaky option to enable config vars during compile:

    heroku labs:enable user-env-compile -a myapp

Set the `NODE_ENV` environment variable (e.g. `development` or `production`):

    heroku config:set NODE_ENV=production

Create your Node.js app and add a Gruntfile named  `Gruntfile.js` (or `Gruntfile.coffee` if you want to use CoffeeScript, or `grunt.js` if you are using Grunt 0.3) with a `heroku` task:

    grunt.registerTask('heroku:development', 'clean less mincss');
    
or

    grunt.registerTask('heroku:production', 'clean less mincss uglify');

Don't forget to add grunt to your dependencies in `package.json`. If your grunt tasks depend on other pre-defined tasks make sure to add these dependencies as well:

    "dependencies": {
        ...
        "grunt": "*",
        "grunt-contrib": "*",
        "less": "*"
    }



NPM Debugging
---------

npm can be run with a verbose flag to help debugging if something fails when installing the dependencies. 

* if the `VERBOSE` environment variable is set, npm is always run with verbose logging.
* if `BUILDPACK_RETRY_VERBOSE` is set, npm is relaunched in verbose mode if npm failed.


Further Information
-------------------

[Heroku: Buildpacks](https://devcenter.heroku.com/articles/buildpacks)

[Heroku: Getting Started with Node.js](https://devcenter.heroku.com/articles/nodejs)

[Buildpacks: Heroku for Everything](http://blog.heroku.com/archives/2012/7/17/buildpacks/)

[Grunt: a task-based command line build tool for JavaScript projects](http://gruntjs.com/)
