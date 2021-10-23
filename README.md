# Overview

in this tutorial you will see how to install inertia js in laravel 8 with vue 3 .
Inertia is a new approach to building SPA(Single page Application) for web.
Inertia has no client-side routing, nor does it require an API.
Simply build controllers and page views like you've always done.

# Create Laravel Project

Installing a fresh laravel project, so head over to the terminal, type the command, and create a new laravel app.
```
composer create-project -d /var/www/code/laravel --prefer-dist laravel/laravel blog "8.*"
```

# Server Side Setup

There is two type of setup to install inertia server side and client side . you need to setup first to server side.

**Install dependencies**

Install the Inertia server-side adapters using the preferred package manager for that language or framework.
```
composer require inertiajs/inertia-laravel
```

**Root template**

Next, setup the root template that will be loaded on the first page visit. This will be used to load your site assets (CSS and JavaScript), and will also contain a root `<div>` to boot your JavaScript application in.
you need to remove the `welcome.blade.php` or rename the `app.blade.php` . then put the code in below
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    <link href="{{ mix('/css/app.css') }}" rel="stylesheet" />
    <script src="{{ mix('/js/app.js') }}" defer></script>
  </head>
  <body>
    @inertia
  </body>
</html>
```
By default the Laravel adapter will use the `app.blade.php` view.
This template should include your assets, as well as the @inertia directive.
If you'd like to use a different root view, you can change it using `Inertia::setRootView()`.


**Middleware**

Next, setup the Inertia middleware. In Laravel you need to publish the `HandleInertiaRequests` middleware to your application, which can be done using this artisan command:
```
php artisan inertia:middleware
```
Once generated, register the `HandleInertiaRequests` middleware in your `App\Http\Kernel`, as the last item in your `web` middleware group.
```
'web' => [
    // ...
    \App\Http\Middleware\HandleInertiaRequests::class,
],
```
This middleware provides a version() method for setting your asset version, and a share() method for setting shared data. Please see those pages for more information.

# Client Side Setup

Once server side setup is done. you need to start config on client side.

**Install dependencies**

Install the Inertia client-side adapters using NPM.
```
npm install @inertiajs/inertia @inertiajs/inertia-vue3
```
Then, install vue 3
```
npm install vue@next
```
In Laravel 8 if you want to compile Vue, before running make sure you have installed "vue-template-compiler" & "vue-loader" with npm.
```
npm install vue-loader vue-template-compiler --save-dev
```
After, or later install full node package. then you can setup your code
```
npm install
```

**Initialize app**

Next, update your main JavaScript file `resources/js/app.js` just like you did before remove code and paste below the code.
```
import { createApp, h } from "vue";
import { createInertiaApp } from "@inertiajs/inertia-vue3";
import { InertiaProgress } from "@inertiajs/progress";
 
InertiaProgress.init();

createInertiaApp({
    resolve: (name) => require(`./Pages/${name}`),
    setup({ el, App, props, plugin }) {
        createApp({ render: () => h(App, props) })
            .use(plugin)
            .mount(el);
    }
});
```
This above code as you can see Pages Folder are name is already define. that way you need to create folder name Pages , you can change the page name if you need. also you can see a already import the progress class.

**webpack.mix.js**

Next, you need to config setup the webpack
```
const mix = require('laravel-mix');

/*
 |--------------------------------------------------------------------------
 | Mix Asset Management
 |--------------------------------------------------------------------------
 |
 | Mix provides a clean, fluent API for defining some Webpack build steps
 | for your Laravel applications. By default, we are compiling the CSS
 | file for the application as well as bundling up all the JS files.
 |
 */

mix.js('resources/js/app.js', 'public/js')
    .vue()
    .postCss('resources/css/app.css', 'public/css', [
        //
    ]);

mix.webpackConfig({
    output: {
        chunkFilename: "js/[name].js?id=[chunkhash]"
    }
});
```

**Create Routes**

Edit `app/routes/web.php`, tt is look like typical  laravel routes the difference is not to view it render the page.
```
<?php

use Illuminate\Support\Facades\Route;
use Inertia\Inertia;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
    return Inertia::render('Home', ['test' => 'working']);
});

Route::get('/about-us', function () {
    return Inertia::render('About', ['about_us' => 'working']);
});
```

**Set Up Front End**

After create routes, then we need to show the data in front end side .
first you need to create Pages Folder, then after create `resources/js/Pages/Home.vue` or other file you want.
Create `resources/js/Pages/Home.vue`, then put the code in below
```
<template>
    <div>
        <h1>Inertiajs is {{ test }}</h1>

        <Link href="/about-us">Go to about us</Link>
    </div>
</template>

<script>
import { Link } from "@inertiajs/inertia-vue3";
export default {
    components: {
        Link,
    },
    props: ["test"],
};
</script>
```
Create `resources/js/Pages/About.vue`, then put the code in below
```
<template>
    <div>
        <h1>inertiajs is {{ about_us }}</h1>

        <Link href="/">Go to Home</Link>
    </div>
</template>

<script>
import { Link } from "@inertiajs/inertia-vue3";
export default {
    components: {
        Link,
    },
    props: ["about_us"],
};
</script>
```
And Final run dev command
```
npm run dev
```
Or if you want to watch command
```
npm run watch
```