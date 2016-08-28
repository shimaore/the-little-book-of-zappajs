title: Getting Started
------

Installation
============

ZappaJS requires Node.js to get started. Node.js can be downloaded from https://nodejs.org/ if it is not integrated in your operating system.

Starting a new project
======================

You should create your new project in a separate directory or folder, in order to be able to benefit from `npm`'s help with dependencies management.

    mkdir my-project
    cd my-project

Best is to first let `npm` configure your project:

    npm init

The initialization script will ask you questions about your new project; if you do not know the answer to a question, hit Enter and `npm` will use sensible defaults.

The `npm init` command will create a `package.json` file in your project's directory. You're welcome to review the file and edit it; however if you modify it you must ensure that it contains valid JSON at all times.

Integrating ZappaJS
-------------------

To integrate ZappaJS into your project:

    npm install --save zappajs

Writing your first ZappaJS
--------------------------

Using your preferred editor, create a file called `server.coffee.md`:

    This file is written using Litterate CoffeeScript, which allows you to specify
    and document your code using the MarkDown format. Text starts on the leftmost
    column, code is preceded with 4 spaces.

    The first step is to load the ZappaJS code into your code:

        Zappa = require 'zappajs'

    You can then use the ZappaJS Domain-Specific Language to build your application.

        Zappa ->

    In this example, we start a new web server (that's what the line above does), which
    will handle one type of HTTP requests (the `GET` method) for a single path ('/'),
    and return the text `Hello world!`.

          @get '/': ->
            @send 'Hello world!\n'

    This is it. Make sure your code is indented with four spaces and you'll be fine!

Finishing the project preparation
---------------------------------

The example above and other examples in this book are written using Litterate CoffeeScript. You should first let `npm` know how to use it:

    npm install --save-dev coffee-script

You should then modify the file `package.json` so that you can automate the startup of your code. Open `package.json` with your text editor; you should see a section:

    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },

Modify it so that it reads:

    "scripts": {
      "prepublish": "coffee -c *.coffee.md",
      "prestart": "npm install",
      "start": "node server.js",
      "pretest": "npm install",
      "test": "mocha"
    },

Starting the web server
-----------------------

To start the web server, run

    npm start

The second command will not terminate as it is running the server process. You can verify it is working by opening a web browser to http://127.0.0.1:3000/ or by using a command-line web downloader such as curl to verify things are working:

    $ curl http://127.0.0.1.:3000/
    Hello world!

Managing your project's code
============================

It is good practice to manage your project using a code versioning system. This allows you to track changes you made in your project, so that you can share them, have other people review them, and be able to track when a problem started by comparing how old code behaved and how new code behaves.

We will show you how to use the `git` versioning system, but you're welcome to figure out which one you prefer. You probably will need to install `git` on your system before doing so, though; we recommend reading the short documents in the [Github Bootcamp](https://help.github.com/categories/bootcamp/) to get you started quickly.

Initializing the versioning system
----------------------------------

To initialize code versioning for your project:

    git init

This will create a `.git` subdirectory which contains information about your project. You're welcome to review its contents; in most cases you will not need to modify it manually, and instead should use the `git` commands.

Recording your first change
---------------------------

Now would be a good time to try `git` out by recording the current state of the file in the versioning system.

    git add .
    git commit

The `git commit` command will prompt you for a description of the changes; the first line should be a short description of the changes, while the following lines can be used to discuss, document, or justify the changes, like so:

    import the original code

    I'm doing this because the little book of ZappaJS told me so!

You can review the history of your changes with

    git log

There are obviously many more `git` commands, along with many graphical interfaces to help you manage larger changes. I personally use both `git-gui` (to record changes) and `gitk` (to review and browse older changes) which are both distributed with `git` itself; but there are [many others](https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools#Graphical_Interfaces).
