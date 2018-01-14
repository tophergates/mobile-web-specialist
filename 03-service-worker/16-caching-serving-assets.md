# 03.16: CACHING AND SERVING ASSETS
It's been fun playing with this stuff, but it's finally time to start doing the real work to make Wittr `offline first`!

So far we've seen how to hijack requests and respond to them differently. We've even created responses ourselves, meaning we can respond without using the network at all. However, if we want to be able to load Wittr without using the network, we need somewhere to store the HTML, CSS, JavaScript, images, web fonts, etc. Thankfully, there is such a place: the `Cache API`.

The `Cache API` gives us the `caches` object on the global. If you want to create or open a cache, you call the `caches.open` method with the name of the cache.

```js
caches.open('my-cache')
  .then(function(cache) {
    // ..
  });
```

This returns a `Promise` for a cache of that name. If you haven't opened a cache with that name before, it creates the cache with that name and returns it.

A cache box can contain request/response pairs from any secure origin. It can be used to store fonts, scripts, images, and anything else really from both our own origin as well as elsewhere on the web.

To add cache items, you can use the `cache.put` method and pass a request, or a URL, and a `Response`. For example:

```js
cache.put(request, response);
```

Or you can use `cache.addAll` which takes an array of requests, or URLs, `fetch`es them and puts the `Response` pairs into the cache. **However, if any of the items in the array fail to cache then none of them are added.**

```js
cache.addAll([
  '/foo',
  '/bar'
]);
```

`cache.addAll` uses `fetch` under the hood, so bare in mind that requests will go via the browser cache.

Later, when you want to get something out of the cache you can call `cache.match` and pass in a request, or URL. This will return a `Promise` for a matching `Response` if one is found, or `undefined` otherwise.

```js
cache.match(request);
```

*Note that the lesson video says this will return `null` if a matching `Response` is not found. This is incorrect. It actually returns `undefined` when a match is not found.*

`caches.match` does the same as `cache.match` except that it tries to find a match in any cache starting with the oldest.

```js
caches.match(request);
```

So we have a way to store items in the cache, but the question is - when should we store it? Thanksfully, there is another `Service Worker` event that helps here.

When a browser runs a `Service Worker` for the first time, the `install` event is fired. The browser will not allow the `Service Worker` to take control of pages until its `install` phase has completed. We can use the `install` event as an opportunity to get everything we need from the network and create a cache for those resources.

```js
self.addEventListener('install', function(event) {
  event.waitUntil(
    // ...
  );
})
```

`event.waitUntil` lets us signal the progress of the `install`. We pass it a `Promise` and then if or when the `Promise` resolves, the browser knows the `install` is complete. If the `Promise` rejects, it knows the `install` failed and this `Service Worker` should be discarded.

- - -

Next: [Quiz: Install and Cache](./17-quiz-install-and-cache.md)