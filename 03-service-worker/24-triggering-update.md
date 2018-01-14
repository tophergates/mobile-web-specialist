# 03.24: TRIGGERING AN UPDATE
We now have a notification appearing, but our goal is to give the user a button they can press to get the latest version. Clicking this button needs to tell the waiting `Service Worker` that it should take over and bypass the the usual `Service Worker` lifecycle.

Then we want to refresh the page, so it reloads with the latest assets from the newest cache. There are three new components that help us achieve this:

A `Service Worker` can call the `self.skipWaiting` method while it is `waiting` or `installing`. This signals that it shouldn't queue behind another `Service Worker`, it should take over right away. We want to call this when the user hits the refresh button in our update notification. But how do we send this signal from the page to the `waiting` `Service Worker`?

Your page can send messages to any `Service Worker` using the `postMessage` method:

```js
// from page:
reg.installing.postMessage({
  foo: 'bar'
});
```

And you can listen for these messages in the `Service Worker` using the `message` event:

```js
// in the service worker:
self.addEventListener('message', function(event) {
  event.data; // { foo: 'bar' }
});
```

So, when the user clicks the refresh button we will send a message to our `Service Worker` telling it to call `skipWaiting`.

Now for the final part; you've already seen `navigator.serviceWorker.controller`, but the page gets an event when its value changes. Meaning a new `Service Worker` has taken over. We're going to use this as a signal that we should reload the page:

```js
navigator.serviceWorker.addEventListener('controllerchange', function() {
  // navigator.serviceWorker.controller has changed!
});
```

- - -

Next: [Quiz: Triggering an Update](./25-quiz-triggering-update.md)