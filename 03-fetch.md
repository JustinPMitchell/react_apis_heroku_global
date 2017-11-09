# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) `fetch`


### Learning Objectives
*After this lesson, you will be able to:*
- Identify the pieces of a `fetch()` call
- Create a React component that calls an API


## Introducing `fetch()`

So... we know what an API is. Now what?

How can we use an API to dynamically manipulate the DOM with the given data? We can use `fetch()`.

In the past, these have been called **AJAX** requests. As you'll come to learn, `fetch()` allows us to build single page applications that do not require refreshes.

**AJAX**, which stands for "Asynchronous Javascript and XML," is the method through which we are able to make HTTP **requests**. The standard requests we will be making are `GET` `POST` `PUT` `PATCH` and `DELETE`.

| Type of Request | What's It Do? |
|-----------------|---------------|
| `GET`  | Read (*'give me movie names from your database'*)|
| `POST` | Create (*'here's a new movie for your database'*)|
| `PATCH` | Update (*'hey, this movie has a new title'*)) |
| `PUT` | Update (*'hey, this movie totally changed'*) |
| `DELETE` | Delete (*'that movie is so bad you should just take it out of the database'*) |

The browser packages this together using `fetch()` and sends it off to a server. The server then listens to your request and provides a **response**. It looks something like this:

![Request/Response](assets/request-response.png)

When you browse to your favorite websites, your browser is making a request and the server is providing a response. `fetch()` allows us to perform the same type of requests over a network. Imagine fetching weather information and rendering it on your website. Perhaps you want to create a real-life Pokedex? You can use `fetch()` to build these applications.

#### Taking a look at fetch in action

That was a lot! Let's take a look at `fetch()` in action.

Imagine we want to `fetch()` the number of astronauts currently aboard the International Space Station (ISS). Good thing there is an API for that, right? This API allows us get the information using the following URL:

```
http://api.open-notify.org/astros.json
```

The API provides a response that looks like the following:

```json
{
	"number": 5,
	"people": [
		{"craft": "ISS", "name": "Oleg Novitskiy"},
		{"craft": "ISS", "name": "Thomas Pesquet"},
		{"craft": "ISS", "name": "Peggy Whitson"},
		{"craft": "ISS", "name": "Fyodor Yurchikhin"},
		{"craft": "ISS", "name": "Jack Fischer"}
		],
		"message": "success"
}
```


> If you'd like, you can copy and paste the API URL into a browser to see this happen.

This particular API tells us the number of people currently in space on the ISS and their names. It also happily gives us "message: success" so we know it worked!

We can fetch this JSON easily using Javascript.

How? The skeleton code looks like this:

```js
fetch(url)
  .then(function(response) {
    // Here you get the data to modify or display as you please
    })
  })
  .catch(function(ex) {
    // If there is any error, you will catch it here
  })  
```

Or, in ES6 syntax:

```js
fetch(url)
  .then((response) => {
    // Here you get the data to modify or display as you please
    })
  })
  .catch((ex) => {
    // If there is any error, you will catch it here
  })  
```

Let's look at what we would apply this for our astronauts:


```js
let issApi = 'http://api.open-notify.org/astros.json';
fetch(issApi)
  .then((response) => {
    return response.json()
  }).then((json) => {
    console.log('JSON from the ISS', json)
  }).catch((ex) => {
    console.log('An error occured while parsing!', ex)
  })
```

Let's break this API call down into a few steps.

* `let issApi = 'http://api.open-notify.org/astros.json'`:
First, we define our API URL to fetch from

* `fetch(issApi)`: We call fetch on that API URL.

* `.then((response) => {
	return response.json()`: We take the response when the server provides it. We return the `response.json()`

* `.then((json) => {
	console.log('JSON from the ISS', json)`: We take that `json` and `console.log` it.

* `catch((ex)`: If an error occurs, we catch it and log it.

That's as simple as fetch is. While there are other ways to handle the response (such as `html` or `blob`), this approach makes writing requests to APIs and other network calls in Javascript easy.

> *Production Warning!* It is important to note that while this is an ES6 standard, [some browsers such as Internet Explorer](http://caniuse.com/#search=fetch) do not support it; yet Edge does. You may need a polyfill for live projects. If you need a polyfill for a production project, [Github's polyfill is very popular](https://github.com/github/fetch).


## Codealong - Shakespeare

It is time for you to build a very simple component that shows a randomly generated Shakespeare poem. We'll do this using the [Shake it Speare API](http://ShakeItSpeare.com/api/poem). Before doing so, challenge yourself to a mini quiz.

<details>
  <summary><strong>Q: Which React.Component method should API calls be made from?</strong></summary>
  <br />
  <p>
    <code>componentDidMount()</code>. Per the <a href="https://facebook.github.io/react/docs/react-component.html#componentdidmount">React documentation</a>, <em>If you need to load data from a remote endpoint, this is a good place to instantiate the network request.</em>
  </p>
</details>

<details>
  <summary><strong>Q: What does it mean to make <code>GET</code> request?</strong></summary>
  <br />
  <p>We are asking the server to send us data to read. To <code>GET</code> means to "read."</p>
</details>

## Fetching Shakespeare in a React Component

Let's go back to your blog project (so make sure it's running!).

Let's use the `fetch()` API directly inside of a React Component to render a poem. We'll be using the `Home` component, so open `Home.js` to edit.

The official [React documentation](https://facebook.github.io/react/docs/react-component.html#componentdidmount) tells developers that any network requests should be placed inside of the _componentDidMount_ method.
* Start by changing the `Home` component to have an empty `componentDidMount()` method.
* Set the stage for returning a poem in the `div` by changing the text to be an `<h1>` with the text "My favorite Shakespeare poem:"

```js
import React, {Component} from 'react';

class Home extends Component {

  constructor(props) {
    super(props)
  }

  componentDidMount() {
  	/* nothing here... yet! */
  }

  render() {
    return (
      <div>
        <h1>My favorite Shakespeare poem:</h1>
      </div>
    )
  }
}


export default Home
```

We can now tell our component to fetch a Shakespeare poem and then set it to our state. We do this by adding the `fetch()` call inside of _componentDidMount()_.

Calling _setState_ then triggers a re-_render_ inside of our component.

**Important:**
The value of the `this` keyword in JavaScript changes depending on what function is being executed. Its relative value depends on the function that contains it.
- The `fetch()` call involves many callbacks with several different functions. In order to preserve the initial context of our React component, we need to create a new variable, `base`, to keep track of the original value of the `this` keyword. Saving the original value of `this` to `base` allows us to access methods like `this.setState()` through `base.setState()` throughout all of the different functions.
- Also important: This variable can be called anything at all; we're just calling it base here. The important bit is just to save `this` to `base`.

So we will:

* Fill in the `componentDidMount` method with calling the API
* Save the poem in a state called `poem`
* Render the poem!

You should have this:

```js
import React, {Component} from 'react';

class Home extends Component {

  constructor(props) {
    super(props);
    this.state = {
      shakeSpeare: ""
    }
  }

  componentDidMount() {
    // save a reference to `this` because the value of `this` will change
    // inside the different callback functions.
    var base = this

    // fetch a poem
    let poemApi = 'http://ShakeItSpeare.com/api/poem';
    fetch(poemApi)
      .then((response) => {
        return response.json()
      }).then((json) => {
          base.setState({ shakeSpeare: json.poem });
      }).catch((ex) => {
        console.log('An error occured while parsing!', ex)
      })
  }

  render() {
    let poetry = this.state.shakeSpeare;
    return (
      <div>
        <h1>My favorite Shakespeare poem:</h1>
        {poetry}
      </div>
     )
  }
}
```

You can test it out at this point - it works!

But just in case, let's add a quick check.
* Add an `if` statement under `render`.
  * This simply checks to be sure that `fetch()` has completed before `render()` tries to return the movie - otherwise it returns "Loading...".
  * For this especially, it's important that the state is declared in the constructor. This way, the `if` statement does not fail if the `fetch()` hasn't created the state yet.

```js

render() {
  	 let poetry = this.state.shakeSpeare;
     if (this.state.shakeSpeare){
       return (
         <div>
           <h1>My favorite Shakespeare poem:</h1>
           {poetry}
         </div>
       )
     }
     return (
       <div>
         <h1>My favorite Shakespeare poem:</h1>
         Loading...
       </div>
     )
  }
```

You're done! Your `home` page should load a random Shakespeare poem!
