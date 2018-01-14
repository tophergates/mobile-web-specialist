# 03.14: HIJACKING REQUESTS 3
We've started handling requests dynamically depending on the URL, but there's a lot more we can do here. In the real world, we need to be a bit more dynamic than this.

The page can send a request, which we intercept and then send to the network. But rather than just sending a `Response` back, we can look at it and then do something else. For example, let's respond with a network `fetch` for the request just as the browser would do. The `fetch` method will take a full request object as well as a URL. `fetch` returns a `Promise`; with `Promises` you can attach a `.then` callback to get the results if the operation was successful. Whatever we return in this callback becomes the eventual value for the `Promise`.

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    fetch(event.request)
      .then(function(response) {
        if (response.status === 404) {
          return new Response('Whoops, not found');
        }
        return response;
      })
      .catch(function(error) {
        return new Response('Uh oh, that totally failed:', error);
      })
  );
});
```

So we can look at the `Response` ourselves and if the `response.status` is 404 (not found) then we can respond with our own message. Otherwise, we return the `Response` we received.

`.catch` is similar to `.then`, but `.then` is for success and `.catch` is for failure. `fetch` will fail if can't make a connection to the server at all, which includes offline. When that happens, we can respond with our own message.

Now if we go to a page that doesn't exist we get the custom message for 404 errors. And if you take the server down and go offline, you get a custom message for that too!

We can create complex rules for requests, trying to get responses from multiple sources and reacting to the results. You can do this on a request by request basis using JavaScript. You can even go to the network and if that fails, get something else from the network.

- - -

Next: [Quiz: Hijacking Requests 3](./15-quiz-hijacking-requests-3.md)