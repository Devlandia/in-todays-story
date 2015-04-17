---
layout: post
title: "Deploying simple html projects to gh-pages using Gulp"
date: 2015-04-10 16:00:00 -0300
comments: true
author: Pedro Martos
categories: [gulp, gh-pages, html pages, node, npm]
---

_by [@pedromartos](https://github.com/pedromartos)_

It all begins when a few weeks ago Fellipe came to me and said that we were going to create Devlandia site. Our site. I was really excited because I knew all the crazy ideas we planned to implement on this project, like to create an AI that could actually interact with the user and help them solve their problems. So basically we're gonna build just a rather very intelligent system. Well...

![its-classified](http://replygif.net/i/1493.gif)

But Fellipe told me that this was just a first draft of our site. Our business card.

So I was going to create a simple HTML page. Bummer. But then he told me that this site should be Fast, awesome and fast!
We should have some cool animations, a smooth scrolling, a contact form that would directly create a card in our Trello, and of course it should be mobile too.

OK...  
![you-had-my-curiosity](http://www.quickmeme.com/img/29/29db411914f980415f23fad69311c356278a6ab55117f4e7d939da2a934d4c4a.jpg)

So now I have this task to create an awesome and fast site.  
At first I thought to use React and Flux somehow but this would be using a bazooka to kill a fly.  
Rails? Bazooka. Fly.  
Then will I have to write a .html file? Will this be openning the Notepad all over again?  
Ok then... :sadface:

![its-not-okay](http://rs2img.memecdn.com/its-not-okay_o_392786.jpg)

I've spent the last few weeks before that studying and researching client-side MVC and React, I must had learned something that I could use here!

## Enters **Gulp**

When I was looking into client-side MVC and React I saw this dude who help everyone the get along together in perfect harmony. **Gulp** is a tool to create automated tasks. If you are a Ruby developer it is something like Rake. If you are a PHP developer it is something like Composer (Actually I don't know exactly. I stopped coding in PHP a long time ago. Now he is just somebody that I used to know).

With Gulp you can create tasks to help you build your projects. I could use it to automatically compile my SASS and CoffeeScript file into CSS and JS, among other awesome things. I'm actually proud that I wrote the `gulpfile.js` all by myself (with a little help from Google of course!)

## So, Gulp. Let's start.

For starters you can use this repo I prepared to quick start simple projects: [Gulp2GHPages](https://github.com/Devlandia/gulp2ghpages).  
Fork it and change its name or just download the master branch inside your own repo. Your call.

Assuming that you already have a github repository ready for your project. Everything begins with the `gulpfile.js`.  
Here you will write all the tasks for your project.

```js
    var gulp = require('gulp');

    gulp.task('foobar', function(){
      console.log("\n\nHELLO WORLD!!!\n\n");
    });
```

Now, in your terminal just enter:

    $ gulp foobar

And BAM. The magic happens.  
For you guys who are not familiar with NPM packages the `require('gulp')` line we are just requiring a package that we installed. Usually this is made via `package.json` file. When you have this file on your folder just run `npm install` and he will install all dependencies that are listed there. See an example [here](https://github.com/Devlandia/gulp2ghpages/blob/master/package.json) and read more about it [here](https://docs.npmjs.com/files/package.json)

Let's kick it up a notch. What if you want to write your stylesheets in **SASS**? Tough? Please.

```js
    var gulp   = require('gulp'),
        concat = require('gulp-concat'),
        sass   = require('gulp-sass');

    gulp.task('sass', function(){
      gulp.src('./src/css/**/*.scss')
          .pipe(sass( { style: 'compressed' } ))
          .pipe(concat('./main.css'))
          .pipe(gulp.dest('./build/css'));
    });
```

Gulp use a concept of streams running through pipes. So our stream is `gulp.src('./src/css/**/*.scss')` which is all .scss files in `./src/css/` folder and subfolders.  
Next we will take the stream and run it through the SASS pipe, and compress each of them `.pipe(sass( { style: 'compressed' } ))`  
Then we'll put them all into a single file called `main.css` using **concat**: `.pipe(concat('./main.css'))`  
Now we just need to tell the stream's final destination: ´.pipe(gulp.dest('./build/css'));´

    $ gulp sass

Aaaaand it's done. You will have all your SASS files compiled, compressed and concatenated into `./build/css/main.css`  
![yeah-coding](http://i.imgur.com/eJ3z7LD.jpg)

And what about **CoffeeScript**? Same.

```js
    var gulp   = require('gulp'),
        concat = require('gulp-concat'),
        uglify = require('gulp-uglify'),
        coffee = require('gulp-coffee');

    gulp.task('scripts', function(){
      gulp.src('./src/js/**/*.coffee')
          .pipe(coffee())
          .pipe(uglify())
          .pipe(concat('./main.js'))
          .pipe(gulp.dest('./build/js'));
    });
```

> Here we added the pipe **uglify** to compress our JS code

So now we know how to compile our **SASS** and **CoffeeScript** files. Everything is fine and dandy. What next?  
How about serving these files? Sure.

Gulp can use a bunch of packages to serve files. So far my favorite is **[BrowserSync](http://www.browsersync.io/)**

```js
    var gulp        = require('gulp'),
        browserSync = require('browser-sync');

    gulp.task('server', function () {
      browserSync({
        files: ['./build/js/*.js', './build/css/*.css', './build/*.html'],
        server: {
          baseDir: './build'
        }
      });
    });
```

Then in your terminal

    $ gulp server

This will start the server on port 3000.  
Observe the line `files: ['./build/js/*.js', './build/css/*.css', './build/*.html']`, it tells **BrowserSync** to keep an eye on these files, so in case they change, the server will automatically reload them.

But this only starts the server, you still need to compile and copy all files to the `./build` folder.

> "So I'll have to run each tasks every time I want to start the server?"

![what-if-i-told-you](http://i.imgur.com/XOCkn7k.jpg)

We can create tasks that can execute others tasks in a row. So let's take the tasks we've created before and put them together under another task

    gulp.task('default', ['sass', 'scripts', 'server']);

To run a `default` task you just need to ender `gulp` in the terminal. This will run sequentially the `sass`, `scripts` and `server` tasks. So now you have your server running with all files properly compiled.

**One more thing.**
Remember when we create the `server` task and we made the server to watch some files so when they change they would automatically be reloaded? Well this won't work the way you imagine. You see the path of those files are `./build/` while you will be working on the folder `./src/`. So when you update your `./src/css/main.css.scss` this will mean nothing to the server. You would have to restart the server to recompile these assets. Which means that line is complete useless, right? No. Not actually.

We just need to create another task that will watch our files on `./src/`. And it goes like this:

```js
    gulp.task('watch', function() {
      gulp.watch('./src/css/**/*.scss', ['sass']);
      gulp.watch('./src/js/**/*.coffee', ['scripts']);
    });
```

Then we update our `default` task to:

    gulp.task('default', ['sass', 'scripts', 'server', 'watch']);

Now right after the server starts Gulp will watch the files we told him. So for example, when we change our `./src/css/main.css.scss`, Gulp will execute the `sass` task and recompile the file `./build/css/main.css` that our server is watching and will reload it (Actually BrowserSync will inject the changes into the main.css that are already loaded on the browser without even reloading the browser. Told you guys BrowserSync is awesome).

And that's it! This is the basic to start you simple html project.

Feel free to look into the [gulpfile.js](https://github.com/Devlandia/gulp2ghpages/blob/master/gulpfile.js) I wrote in this [boilerplate repository](https://github.com/Devlandia/gulp2ghpages). I've create a lot of tasks to take care of other things like compiling [Jade](http://jade-lang.com) files into HTML files.

## Hey! And what about deploying to GH-Pages?

Oh, yeah. Almost forgot about that. Very simple. We just create another task.

```js
    var gulp    = require('gulp'),
        ghPages = require('gulp-gh-pages');

    gulp.task('deploy', ['sass', 'scripts'], function() {
      return gulp.src('./build/**/*')
                .pipe(ghPages());
    });
```

This `deploy` task will take all files inside `./build/` and deploy them to a gh-pages branch of your repository when you run the task in your terminal

    $ gulp deploy

And just like that will you have your repository hosted on GH-Pages.

![clap-clap-clap](http://media.giphy.com/media/ZxrpYTevDLmmY/giphy.gif)

_'Till next time._

