Global Assets
===============
City of Malmö’s external assets are served from a central asset host and are consumed by web applications on the organizations web sites. The assets contains a common base for the UI such as a masthead, footer, form styling, page layouts, styling for articles, widgets, and components.

Purpose of the Global Assets:

* It gives all web applications a consistent UI for the user.
* Common changes are deployed in one place and the changes are reflected immediately in all web applications.
* It decreases the load time for the end user since the majority of stylesheet and Javascript code will be cached in the browser instead of being fetch from each application.
* It speeds up development.

Apart from the common assets consumed by all applications, each application contains it’s own unique assets for additional features not included in the common assets. The application specific assets are served by the applications themselves.

For more information, contact webbteamet@malmo.se.

## How to Use the Assets
Instructions for how you use the global assets in your application is documented in City of Malmö's [Web Application Guidelines](http://malmostad.github.io/wag/).

## Dependencies for Development and Deployment
* Ruby 2.0
* Rubygems and Bundler
* [Grunt](http://gruntjs.com/) for Grunticon SVG and PNG management
* [Bower](http://bower.io) to update the [`shared_assets`](https://github.com/malmostad/shared_assets)

## Dependencies for Asset Hosting
A web server optimized to serve static files. Ruby is not needed on the server.


## Development Setup
The assets are developed using Sprockets and the Asset Pipeline in a stripped down Ruby on Rails-application. The application is only used for development, testing, build and deployment. You can use the application as a local asset host when you are developing or adapting other applications to the global assets.

To install the required Ruby gems, run:

```
$ bundle install
```

Start the local asset server:

```
$ rails s -p 3001
```

You might need to map a domain name to your local host like `www.local.malmo.se` to debug and test the JavaScript code.

Use the local views in the asset application for the visual part of the development. You can also point another locally installed web application your started asset server.

## Build and Deployment
Capistrano is used for build and deployment to the server from your local copy. Include the `AUDIENCE=internal|external` in the deployment command. `internal` is for the intranet version and `external` for public web sites.

```
$ AUDIENCE=internal bundle exec cap staging deploy
$ AUDIENCE=internal bundle exec cap production deploy
$ AUDIENCE=external bundle exec cap staging deploy
$ AUDIENCE=external bundle exec cap production deploy
```

The compression levels for CSS and Javascript files are configured in `config/environments/` for development, staging and production.


## Build Icons
[Grunticon](https://github.com/filamentgroup/grunticon) is used to compile SVG icons into CSS files with PNG fallbacks. Master SVG files is in `masters/icons` and the output goes to `app/assets/icons`. To setup Grunticon, make sure you have [Grunt](http://gruntjs.com/) installed and then run the following command in the project root:

```
npm install
```

To rebuild all icons, run:
```
grunt
```

A preview of the icons is generated in `app/assets/icons/preview.html`.


## Development

The assets contains Sass code compiled to CSS, Coffeescript compiled to JavaScript, web fonts and an icon font.

Fundamentals for the Global Assets:

* They must be highly optimized for load, execution and rendering speed.
* Have a long life cycle. The code from the asset host must be backward compatible for a long time since a lot of web application are consuming them and can’t be updated on a continues basis.
* There is no room for extensive testing of all consuming web applications. It just have to work.

### The Development Application
The Global Assets are developed using Sprockets and the Assets Pipeline in a stripped-down Ruby on Rails application. It is used in development and deployment but not in production. It can be used as a local asset server during development and when developing or adapting other applications for the The City of Malmo.

You must set a fully qualified domain name in the host file for your local development environment. such as `www.local.malmo.se`.

### Stylesheets
Stylesheets are written in Sass with the SCSS syntax and organized in smaller files. All files, except for IE specifics, are imported in the `malmo.css.scss` file. The files are served uncompressed in development and concatenated to one compressed file, `malmo.css` with a Rake task by Capistrano in the build process.

All sizes must be set in `em` or percentage units. Use the `emize()` Sass function from [`shared_assets`](https://github.com/malmostad/shared_assets) to get a consistent output for font sizes. Thin border widths can be declared with `1px`.

Do not set font sizes in global contexts, e.g. for all menus or for all `p` or `h1` elements. Specify it on a detailed level to avoid overrides and complex calculations that will break when the context is changed.

Do not use too deeply nested Sass blocks, this will make it hard to override the selectors in media queries.

The `columns()` mixin from [`shared_assets`](https://github.com/malmostad/shared_assets) is used to define responsive column layouts that and is altered in the media query file.

### Coffeescript
Use Coffeescript for JavaScript development and organize it in smaller files that will be imported in the malmo.js file using the Sprockets syntax. The files are automatically served as individual files in the development application for easy debugging and concatenated and compressed in the build phase.

### The Masthead and the footer
The common masthead and the footer that must be on every application page is found in `app/assets/content/[internal|external]masthead.html.erb`. The code is transform to a Javascript string during the Capistrano build process for fast injection on every page or view. They are also built when you launch the servern in development mode.

### Icons
Use SVGs icons, not png images or sprites.

### Gradients, Rounded Corners and Shadows
For gradients, rounded corners and related stuff, use CSS3. Use solid background colors as a fallback. Use the Sass mixins for vendor prefixes to keep the code clean.

### Frameworks and Third Party Code
Third party code resides in `vendor/assets`. The code is included in the global stylesheet and Javascript file in the development environment as well as in the build process.

Third party code and components are added to the Global Asets with caution, no brute force incorporation of frameworks and code without qualification. The City of Malmo’s Glboal Assets is an enterprise level solution and must be maintained with all consuming applications in mind.

## License
Released under AGPL version 3.

The `vendor` directory contains third party code that may be released under other licenses stated in the start of each file.
