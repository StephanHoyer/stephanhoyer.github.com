title: Developer Blog using wintersmith and github pages
author: stephan-hoyer
date: 2012-11-15 8:11
template: article.jade
tags: node.js

For a long time, I want to be able to share some thoughts about live in a
blog every now and than. I made serveral attempts to do so. I tried
[tumblr](http://tumblr.com) but it wasn't as flexible as i need it. I tried 
[octopress](http://octopress.org), but i'm not as
familiar with ruby as I probably should be. Never tried wordpress, since it
is to much overhead and written in bad ol' php, which I want to avoid for
reasons. Also I want to write my posts using a simple markup language like
[markdown](http://daringfireball.net/projects/markdown/) or 
[restuctured text](http://google.com), 
first for versioning in git, second for using my beloved vim for editing. 

Since I use node.js a while now, i searched for
ports of octopress in node and found
[wintersmith](https://github.com/jnordberg/wintersmith), what i'm using now.

It has great flexibility, is super simple to use and to extend. It works good
on [github pages](http://pages.github.com/), what i use as hosting.

## Setup

So here is how to setup. First you have to install
[**node.js**](http://nodejs.org) as showed on the node.js
[homepage](http://nodejs.org). Because of the awesomenes of 
[**npm**](http://npmjs.org), installing wintersmith is easy:

```
$ npm install wintersmith -g
```

That gives you - besides the library - a binary where you can run serveral
tasks. First one is to create your blog:

```
$ wintersmith new path/to/my-shiny-new-blog
```

That creates a file stucture and some basic posts for you:

```
├── config.json                site configuration and metadata
├── contents
│   ├── archive.md
│   ├── articles               each article is a subfolder of articles
│   │   ├── another-test
│   │   │   └── index.md
│   │   ├── bamboo-cutter
│   │   │   ├── Taketori_Monogatari.jpg
│   │   │   └── index.md
│   │   ├── hello-world
│   │   │   └── index.md
│   │   └── red-herring
│   │       ├── banana.jpg
│   │       └── index.md
│   ├── authors                if an author is set in an articles metadata it
│   │   ├── baker.json         will be read from here
│   │   └── the-wintersmith.json
│   ├── css
│   │   ├── github.css
│   │   └── main.css
│   ├── feed.json              json page that renders the rss feed to feed.xml
│   ├── index.json
└── templates
    ├── archive.jade
    ├── article.jade
    ├── author.jade
    ├── feed.jade
    ├── index.jade
    └── layout.jade
```
You can see that it really takes a few files to drive a wintersmith blog. Most
of them are templates and the blog posts itself. Besides that there are config
and CSS files.

Since _github_ serves the page from the base dir of the repo, we have to move
the blog to a new directory I called `src`

```
$ mkdir src
$ mv config.json contents templates src
```

We want to see the blog in action, right. Cd to the `src` directory and type

```
$ wintersmith preview
```

and head to http://localhost:8080 in the browser. If all went right, you should see a
blog with two example entries.

You want to test deployment to github, so you have to build the static
html-files for our blog. In order to do so you have to change the build 
directory to be the root dir of the repository. Therefor go to `config.json`
in the `src` folder and add the following line above the `locals`-section:

```
  "output": "../",
```

It tells wintersmith to create the files in our blog root directory which will
be our git root. Then you can start the build process:

```
$ wintersmith build
```

If you didn't already have, create a git repository in the
blog directory. Then commit all your changes and push them to your shiny new
created github repository called `your-github-name.github.com`.

After _github_ will send you a notification mail, if your website has benn
deployed. Then you can visit the new blog under
http://your-github-name.github.com.

Now you can write your first blogpost. Simply create a new directory under
`src/articles` or use one of the example posts and change it appropriatly.

## Tweeks

### Base URL

As webdeveloper you now the problems that occur when using relativ links in
images, css and js files and of cause links. Thats why you always want to uses
absolute urls. Wintersmith provides an environment variable called
`locals.url` what you can use in templates. The problem is, that it has to be
different in preview mode (http://localhost:8080) and build mode
(http://your-github-name.github.com). I solved this using two different config
files, the existing on for preview, and a treaked one for build. When calling
to build the site, you can specify a custom config file to use:

```
$ wintersmith build -c config.build.json
```

### Using Stylus

Since there are CSS preprocessors out there, you didn't want to work with
plain CSS anymore, right? That's a problem already solved by
`wintersmith-perian` plugin. Simply install it via npm

```
$ npm install wintersmith-perian -g
```

After that you only need to tell wintersmith, that there is a new plugin to
load by altering the config file and adding

```
  "plugins": [
    "wintersmith-perian"
  ],
```

above the `locals`-object. From now on, all files named `*.styl` will be compiled
to CSS live in preview mode and also during build step.

Hope this helps setting up the next awesome developer blog.
