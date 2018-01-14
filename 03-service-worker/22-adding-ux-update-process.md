# 03.22: ADDING UX TO THE UPDATE PROCESS
I think we can safely say that we have completed unobtrusive app updates in the last section. Now, we need to get the user onto the latest version quickly and painlessly.

  - [X] Unobtrusive App Updates
  - [ ] Get the user onto the latest version
  - [ ] Continually update cache of posts
  - [ ] Cache photos
  - [ ] Cache avatars

Ideally we want the user to be on the latest version as soon as possible. But as we saw, when a new `Service Worker` is discovered it waits until aol pages using the current version go away before it can take over. This can take a long time, so lets do something better.

Our goal is to tell the user once an update has been found and give them a button to ignore it, or to refresh the page to get the new version.

When you `register` a `Service Worker`, it returns a `Promise`. That `Promise` fulfills with a `Service Worker` registration object. This object has properties and methods relating to the `Service Worker` registration.

```js
navigator.serviceWorker.register('/sw.js')
  .then(function(registration) {
    // ...
  });
```

We get methods like `unregister` and `update` as well as three properties: `installing`, `waiting`, and `active`. These will point to a `Service Worker` object and give insight into the `Service Worker` lifecycle.

The registration object will emit an event when a new update is found called `updatefound`:

```js
navigator.serviceWorker.register('/sw.js')
  .then(function(registration) {
    registration.addEventListener('updatefound', function() {
      // registration.installing has changed
    });
  });
```

On the `Service Worker` objects themselves, you can look at their `state`:

```js
var sw = registration.installing;
console.log(sw.state); // "installing"
```

The `state` can be:
  - installing - The install event has fired but hasn't yet completed.
  - installed - Installation completed successfully, but hasn't yet activated.
  - activating - The activate event has fired, but is not yet complete.
  - activated - The Service Worker is ready to receive fetch events.
  - redundant - The Service Worker has been thrown away. This happens when the Service Worker has been superceded by a new Service Worker or fails to install.
  
The `Service Worker` fires the `statechange` event whenever the value of the `state` property changes.

```js
sw.addEventListener('statechange', function() {
  // sw.state has changed
})
```

Also, `navigator.serviceWorker.controller` refers to the `Service Worker` that controls this page.

We want to tell the user when there is an update ready, but because the `Service Worker` update happens in the background this update could be ready and waiting, in progress, or it may not have started yet. This means we need to look at the `state` of things when the page loads as well as listen for future changes.

For instance, if there is no `controller`, that means the page didn't load using a `Service Worker` - it loaded from the network. Otherwise, we need to look at the `registration`.

If there is a `waiting` `Service Worker`, then there is an update ready and we need to notify the user. Otherwise, if there is an `installing` `Service Worker` then there's an update in progress. Of course that update may fail, so we listen to the `statechange`s to track it and if it reaches the `installed` `state` we tell the user:

```js
if (!navigator.serviceWorker.controller) {
  // page didn't load using a service worker
}

if (registration.waiting) {
  // there's an update ready!
}

if (registration.installing) {
  // there's an update in progress
  registration.installing.addEventListener('statechange', function() {
    if (this.state === 'installed') {
      // there's an update ready!
    }
  });
}
```

Otherwise, we listen for the `updatefound` event. When that fires, we listen to the `statechange`s of the `installing` `Service Worker` and if it reaches the `installed` `state` we tell the user:

```js
registration.addEventListener('updatefound', function() {
  registration.installing.addEventListener('statechange', function() {
    if (this.state === 'installed') {
      // there's an update ready!
    }
  })
})
```

That's how we can tell the user about updates whether they're already there, in progress, or start sometime later.

- - -

Next: [Quiz: Adding UX](./23-quiz-adding-ux.md)