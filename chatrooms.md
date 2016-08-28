title: First Application: chatroom
-----

Writing the code
----------------

We assume you set up your project as described in the previous section; to summarize:

    mkdir my-project
    cd my-project
    npm init
    npm install --save zappajs
    npm install --save-dev coffee-script

Modify `package.json` so that it contains:

    "scripts": {
      "prepublish": "coffee -c *.coffee.md",
      "prestart": "npm install",
      "start": "node server.js",
      "pretest": "npm install",
      "test": "mocha"
    },

Test manually:

    npm start
    # in a separate shell/window
    curl http://127.0.0.1:3000/

Record in code versionining:

    git init
    git add .
    git commit

Code
----

    We will be using ZappaJS

        Zappa = require 'zappajs'

    Start a server using the default parameters (especially port 3000), and configure
    that server with the Zappa Domain-Specific Language (DSL)

        Zappa ->

    In this example we will be using the `client` plugin

          @with 'client'

    We will be sharing data between Express and Socket.io using a `session` object,
    so we need to make sure the two can talk together.

          @use session:
            store: new @session.MemoryStore()
            secret: 'a cat told me...'
            resave: true
            saveUnitialized: true

In this example we use a basic memory store for the session data, which is great for examples, but will never release memory. We also use a very simple secret key. In a real-world application, you would use something more like this:

          redis_configuration =
            host: 'localhost'
            port: 6379
            secret: 'c9acf669-23c4-47f0-979b-7b5d31c24d69'

          Store = (require 'connect-redis') @session
          @use session:
            store: new Store redis_configuration
            secret: redis_configuration.secret
            resave: true
            saveUnitialized: true

This will use a redis-server for storage of the session data. A lot of other storage options are available (FIXME: url).

Moreover, in a real-world application with multiple servers running the same ZappaJS server, you would want to be able to broadcast messages across all servers, like so:

          Adapter = require 'socket.io-redis'
          @io.adapter Adapter redis_configuration

Now let's go back to the main ZappaJS code:

    First we will provide an HTML page for a browser to load; the `render` functionality
    is provided by ExpressJS, and it supports a wide range of templating languages. In this
    case the only parameter we provide to the template is the title for the page.

          @get '/': ->
            @render 'index',
              title: 'PicoChat room!'

    In this example we will use the templating language integrated in ZappaJS, which is called `teacup`.
    First we load the HTML tags we will need, they are provided by `teacup`:

          {doctype,html,head,title,script,body,div,form,input,button} = @teacup

    The HTML content is generated when the `render` command is ran. Inside a `teacup` template rendered
    by ZappaJS, `@` or `this` refers to the context provided by the `render` command.

          @view index: ->
            doctype 5

    Notice how we use the double-arrow `=>` in order to let the context of the rendering propagate down
    so that `@title` still refers to the proper context.

            html =>
              head =>
                title @title
                script src: '/index.js'

    Here we do not need to use double-arrows since we do not use `@` or `this` from the rendering context.

              body ->
                div '#panel'
                form ->
                  input '#box'
                  button '#sendButton', -> 'Send Message'
                  button '#changeButton', -> 'Change Room'

    Notice how the HTML head contains a reference to a JavaScript module available at `/index.js`.
    The `client` plugin lets us write our client code (the code that will be executed in the
    web browser) directly alongside the server code. It's important to understand this code will be
    run in the browser, not on the server, though.

    Here, we let ZappaJS create the `/index.js` JavaScript file; the code is built using tools
    that allow to re-use modules the same way Node.js allows us to do it. The code is built only
    once, and everything is packed inside `/index.js`, making it available rapidly to the web
    browser.

          @client '/index.js': ->

    We use the small `component-dom` module instead of a larger project such as jQuery to keep the
    download small.

            $ = require 'component-dom'

    The `client` plugin already uses the `zappajs-client` module, and we can use Socket.io inside
    the web-browser to talk to the server.

    Here, when the server sends us a `said` message, we append its content to the HTML object
    with the ID 'panel'. Note how the content of the message is made available by `zappajs-client`
    in the `@data` object.

            @on said: ->
              $('#panel').append "<p>#{@data.nickname} said: #{@data.msg}</p>"

    Conversely, when the browser code starts, we send two messages to the server:

            @emit 'set nickname': {nickname: prompt 'Pick a nickname!'}
            @emit 'set room': {room: prompt 'Pick a room!'}

    And focus the input box:

            $('#box').focus()

    Finally we provide interaction for the two buttons:

            $('#sendButton').on 'click', (e) =>
              @emit said: {msg: $('#box').value()}
              $('#box').value('').focus()
              e.preventDefault()

            $('#changeButton').on 'click', (e) =>
              @emit 'set room': {room: prompt 'Pick a room!'}
              $('#box').value('').focus()
              e.preventDefault()

This finishes the client-side code.

Now that the client-side code is written, we know that we will need to handle 3 messages on the server: `set nickname`, `set room`, and `said`, so still inside the same file we write the following code.

    Note how `zappajs` uses the same conventions as `zappajs-client` and makes the content of the messages available inside the `@data` object.

          @on 'set nickname': ->
            @session.nickname = @data.nickname
            @emit 'said',
              nickname: 'moderator'
              msg: 'Your name is ' + @session.nickname

          @on 'set room': ->
            @leave(@session.room) if @session.room
            @session.room = @data.room
            @join(@session.room)
            @emit 'said',
              nickname: 'moderator'
              msg: 'You joined room ' + @session.room

          @on said: ->
            data =
              nickname: @session.nickname
              msg: @data.msg
            @broadcast_to @session.room, 'said', data

This concludes the first example. This short code provides a fully distributed, multi-room chat service, which can be scaled (using the Redis configuration) and where different servers can play different roles (providing the HTML code or running the messaging).

Using the code
--------------

Note that this code uses the `client` plugin for ZappaJS, and that the client-side uses the `component-dom` module, so you will have to run

    npm install --save zappajs-plugin-client component-dom

to make them available.

You can then run

    npm start

and start using the chat service on http://127.0.0.1:3000/ .
