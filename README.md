React Natural Starter
========

The simplest framework to build your isomorphic application.

Standing on the shoulders of giants like [React](http://facebook.github.io/react/) and [React-Router](https://github.com/rackt/react-router), React Natural gives you a simple set of conventions to build your application.

##How to start
Simply clone the official starter project (aka this repo) and install dependencies:

    git clone https://github.com/framp/react-natural-starter.git
    npm install
    npm run watch

Congratulations, you're now running an isomorphic application!

In order to customize your application you can add a `page` and a `route` to the system.

##Adding a page
Create a new directory in `./pages/new-route` containing `data.js` and `view.jsx`.
`data.js` is a function run on the server which return data or execute an action.
It accepts a [express](https://github.com/strongloop/express) request object and a callback, which has to be called with the data you want to return to the client.

    module.exports = function(req, next){
      next({
        title: 'New Route',
        name: req.body.name || 'framp'
      })
    } 

You can use `data.js` to communicate with an API server (where the business logic live).
React Natural server and client are strictly coupled: it's not a good idea to put your business logic here.

`view.jsx` is a simple React component which is used to render the page, both on the server and on the client.

    'use strict'
    let React = require('react')
    let Link = require('react-router').Link
    let Form = require('react-router-form')

    module.exports = React.createClass({
      getInitialState: function() {
        return this.props.initialState || { 
          name: 'Unknown'
        }
      },
      componentDidMount: function() {
        document.title = this.state.title
      },
      render: function () {
        return (
          <div>
            Hello {this.state.name} <br />
            <Form method="POST" to="search">
              My name is: <input type="text" name="name" /> <input type="submit" />
            </Form>
            <Link to="root">Back to home</Link>
          </div>
        )
      }
    })

We can use `Link` and `Form` elements to interact with user - exactly like you do with the boring `<a>` and `<form>` from HTML.
They just have a little bit of Ajax magic which makes everything refresh-less.

##Adding a route

After declaring your page, you probably want to see what you did.

Edit the `routes.jsx` file and add your `new-route` to the application.
These routes will be run both on server and on the browser.

    module.exports = [
      <Route name="root" path="/" handler={require('./pages/root/view')}></Route>,
      <Route name="new-route" path="/new-route" handler={require('./pages/new-route/view')}></Route>,
    ]
    
Take a look at the React Router [documentation](http://rackt.github.io/react-router/) for more information.

##Changing <head> template

To generate everything but body of your application we define another React component.

Edit `template.jsx` and edit the template inserting everything you need.

    'use strict'
    let React = require('react')

    module.exports = React.createClass({
      getInitialState: function() {
        return this.props.initialState || {}
      },
      render: function () {
        return (
          <html>
            <head>
              <title>{this.state.title}</title>
              <script src="https://fb.me/react-0.13.3.min.js"></script>
              <script dangerouslySetInnerHTML={{
                __html: 'if (!window.React) { document.write(\'<script src="/js/react-0.13.3.min.js"><\' + \'/script>\'); }'
              }} />
              <script src="/js/react-router-custom.js"></script>
              <script dangerouslySetInnerHTML={{
                __html: 'initialState=' + JSON.stringify(this.state)
              }} />
              <script src="/js/app.js"></script>
            </head>
            <body>
              {'{body}'}
            </body>
          </html>
        )
      }
    })

You just have to respect 2 rules:

  - Don't touch the body (or React will complain)
  - Don't remove the pre-existing scripts (or you'll lose your client-side React)
  
The `{'{body}'}` string will be replaced with the components defined in your pages

##Advanced edits
Every React Natural application has 2 fundamental entrypoints:

 - `clients.jsx`: is a light wrapper on the client implementation provided by React Natural
 - `server.jsx`: is an express application which uses the server middleware provided by React Natural

If you want to add server side routes you can simply edit `server.jsx` following [express](https://github.com/strongloop/express) documentation.

##I would've made it differently!
Almost everything is customizable in React Natural: if you feel the need to change the client just check out the docs for the original one and make your own version.
[React Natural](http://github.com/framp/react-natural/) is so small you'll probably understand the code in the time it takes you to drink a coffee.

##Coffee
Talking about coffee, feel free to refill my cup with a [click](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=BDPUGENG892JA) and keep me awake working on Open Source Projects.
Also, if you're looking for a contractor in London, [drop me a line](mailto:hi@framp.me).

##License
MIT
