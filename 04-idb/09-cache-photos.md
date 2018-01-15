# 04.09: CACHE PHOTOS

Some of the posts on Wittr have photos with them, we want to cache those images too. At the moment, we're only caching resources at `install` time, whereas the images appear over the lifetime of the application with the posts. So we want to cache images as they appear.

We could put these images in `IDB` along with the rest of the post data, but that means we'd need to read the pixel data and convert it into a `blob` and that kind of complicated and it also loses streaming, which has a performance impact.

When we get an item from a database, we have to take the whole thing out in one lump then convert it into image data and add it to the page. Whereas, if we get the image from a cache it will stream the data so we don't need to wait for the whole thing before we display anything. This is more memory efficient, and leads to faster renders. For that reason, the `Cache API` is a much better fit.

Since we're into the advanced stages of the course, this hasn't been made totally straightforward. Here is the code for the responsive image:

```html
<img src="/photos/65152-800px.jpg"
  srcset="/photos/65152-1024px.jpg 1024w,
          /photos/65152-800px.jpg 800w,
          /photos/65152-640px.jpg 640w,
          /photos/65152-320px.jpg 320w"
  sizes="(min-width: 800px) 765px,
         (min-width: 600px) calc(100vw - 32px),
         calc(100vw - 16px)">
```

Because images can appear at a variety of different widths, this responsive image lets the browser decide which image to load based on the width of the window and also the network conditions. So, when the post arrives through the `WebSocket`, which version do we cache?

Well, we wait until the browser makes the request. Then we hear about it in the `Service Worker`. We go to the network for the image and once we get a response, we put it in the cache. At the same time, we send it on to the page.

Note that we'll put the image into a separate cache to the rest of the static content. We reset the content of our static cache whenever we update our JavaScript or our CSS, but we want these images to live between versions of our application.

Next time we get a request for an image that we already have cached, we simply return it. But, here's the trick; we'll return the image from the cache even if the browser requests a different size of the same image. Posts on Wittr are short-lived, so if the browser requests a bigger version of the same image returning a smaller one from the cache isn't really a problem. Returning a bigger image than the browser asked for is perfectly fine too - we're not wasting bandwidth by doing that. In fact, getting a smaller version of something we already have cached would be a waste of bandwidth. Also, resizing the browser window back and forth is only really something that web developers do.

At this point, we've covered most of the APIs you'd need to be able to cache images. There's only one thing left to cover. You can only use the body of a `Response` once. As in, if you read the `Response`s `json`, you cannot then read it as a `blob`. The following example would fail:

```js
fetch('/foo').then(function(response) {
  var jsonData = response.json();
  var blobData = response.blob(); // fails
});
```

This is because the original data has been used already by the time you attempt to read it as a `blob`. Keeping it around in memory would just be a waste. Also, `respondWith` uses the body of the `Response` as well, so you cannot later read it again:

```js
fetch('/foo').then(function(response) {
  var jsonData = response.json(); // response consumed
  var blobData = response.blob(); // fails
  event.respondWith(response); // also fails
});
```

In most cases, this is great because if the response was a huge video file (like 3GB) the browser doesn't need to keep the whole 3GB in memory - it only needs to keep the bit that is currently playing, plus a bit extra for buffering. However, this is a problem for our images. We want to open a cache, fetch from the network, and send a response to both the cache _AND_ back to the browser. But, using the `Response` body twice like this doesn't work:

```js
event.respondWith(
  caches.open('wittr-content-imgs').then(function(cache) {
    return fetch(request).then(function(response) {
      cache.put(request, response); // response consumed here
      return response; // fails :(
    });
  })
);
```

Luckily, we can fix this issue by cloning the `Response` we send to the cache with the `clone` method:

```js
event.respondWith(
  caches.open('wittr-content-imgs').then(function(cache) {
    return fetch(request).then(function(response) {
      cache.put(request, response.clone());
      return response;
    });
  })
);
```

Now our `clone` goes to the cache and the original get's sent back to the page. The browser keeps enough of the original request around to satisfy all of the `clone`s.

---

Next: [Quiz: Cache Photos](./10-quiz-cache-photos.md)
