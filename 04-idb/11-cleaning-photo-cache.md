# 04.11: CLEANING PHOTO CACHE

Caching the photos is great, but like we saw with the database earlier you can't just keep adding stuff to the cache. We'll need to remove stuff that we don't need anymore.

This involves a couple of cache methods that we haven't seen yet. If we want to remove specific entries from the cache, we can use `cache.delete`, passing in the URL or the request of the thing we want to delete:

```js
cache.delete(request);
```

There's also a `cache.keys` method that returns a `Promise` which gives us all the requests for entries in the cache:

```js
cache.keys().then(function(requests) {
  // ...
});
```

What hasn't been mentioned so far is that all of this is available from pages, as well as `Service Workers`. Let's have a look at using these methods to clean the image cache in the next chapter.

---

Next: [Quiz: Cleaning Photo Cache](./12-quiz-cleaning-photo-cache.md)
