# 03.12: HIJACKING REQUESTS 2
Now that we know how to hijack a request and respond with basic HTML, let's do something cooler...

Let's go to the network for the response, but not for the thing that was requested. There is an API to achieve this: `fetch`.

You might be thinking: "Isn't this what XMLHttpRequest" is for?" No, just... no! Much of the XHR API is 15 years old! Even from the outset, it wasn't particularly well thought out.

As an example, here's the code to fetch some JSON from the URL `/foo`:

```js
var client = new XMLHttpRequest();

client.addEventListener('load', function() {
  console.log(client.response);
});

client.addEventListener('error', function() {
  console.log('It failed');
});

client.responseType = 'json';
client.open('GET', '/foo');
client.send();
```

Everything just feels like it is in the wrong order. The URL is at the bottom, you have to open before you send for some reason, it makes you declare how the response is read before you make the request, and it doesn't support lower-level things like streams. But worst of all the event system gets you into 'callback hell.'

Here's the fetch code for the same operation:

```js
fetch('/foo')
  .then(function(response) {
    return response.json();
  })
  .then(function(data) {
    console.log(data);
  })
  .catch(function(error) {
    console.error('An error occurred:', error);
  });
```

Fetch returns a promise that resolves to the response. Then we can read the response as JSON, and then do something with the results. We can catch errors from either the request, or reading the response. DONE!

As it turns out, back in our Service Worker, `event.respondWith` takes either a `Response` or a `Promise` that resolves to a `Response`. Fetch returns a `Promise` that resolves to a `Response`, so they compose together really well.

Let's respond with a `fetch` for a `.gif` file:

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    fetch('/imgs/dr-evil.gif')
  )
})
```

We've just served up different content using the network!

The Fetch API performs a normal browser fetch, so the results may come from the cache. That is a benefit in this case as we want the `.gif` to cache as usual.

- - -

Next: [Quiz: Hijacking Requests 2](./13-quiz-hijacking-requests-2.md)