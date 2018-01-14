# 03.10: HIJACKING REQUESTS
So far, we have:

  - Setup the Service Worker
  - Learned the Service Worker lifecycle
  - And explored Chrome Developer Tools
  
But we haven't really used the Service Worker to do anything yet. We have seen requests go from the page, through the Service Worker fetch event, and onto the network through the HTTP Cache.

Now, we're going to catch the request as it hits the Service Worker and respond ourselves so nothing goes to the network. This is an important step in going `offline first`.

Over in the Service Worker script file located at `public/js/sw/index.js`, we will tell the browser that the Service Worker is going to handle the request by listening for a fetch event and using the `respondWith` method on the event object:

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    // ..
  );
});
```

The `respondWith` method takes a response object or a `Promise` that resolves with a response. One way to create a response is to create a new `Response` instance:

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    new Response('Hello, World!')
  )
});
```

The first parameter of `Response` is the body of the response. **Notice there is no semi-colon at the end since the response is a parameter of the `respondWith` method.**

The default content-type sent back to the browser is `text/plain`, so you will need to change the content-type sent back depending on the type of content that you are sending back as the response.

Luckily for us, we can set headers as part of the response. The second parameter of `Response` is an object which has a `headers` property with an object of key/value pairs to send back as the response headers:

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    new Response('Hello, World!', {
      headers: {
        foo: 'bar'
      }
    })
  )
})
```

Refresh the page and look at the `Network` tab in Chrome developer tools, take a look at the response headers and you will see that `foo: bar` was returned as part of the response headers.

- - -

Next: [Quiz: Hijacking Requests 1](./11-quiz-hijacking-requests-1.md)