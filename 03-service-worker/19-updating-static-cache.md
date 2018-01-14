# 03.19: UPDATING THE STATIC CACHE
To get started, here is the todo list that gets us from where we are now to full `offline first` success:

  - [ ] Unobtrusive App Updates
  - [ ] Get the user onto the latest version
  - [ ] Continually update cache of posts
  - [ ] Cache photos
  - [ ] Cache avatars

The remainder of the course will be about completing each of these todo items, starting with updating the application.

Head over to Chrome developer tools and disable the `Update on reload` option in the `Application` tab. This will enable us to see how actual users will experience the application.

If a change is made to the application, such as updating the CSS and the page is reloaded now - the application will not update. The reason is because our cache still contains the old CSS. The cached CSS is updated as part of the `install` step of the `Service Worker`, but that isn't happening because there is no new `Service Worker` waiting to `install`.

In order for the browser to pick up these changes you need to make a change to the `Service Worker`. The browser will then treat the `Service Worker` as a new version waiting to be installed. Because of this, the `Service Worker` will receive its own `install` event where it will `fetch` the requested files and put them in a new cache. It won't do this automatically, we have to change the name of our cache. We create a new cache, because we do not want to disrupt the cache that already exists being used by the old `Service Worker` and the pages it controls.

Once the old `Service Worker` is released, we delete the old cache so the next page load gets resources from the new cache; meaning it gets the latest CSS, JavaScript, images, etc.

We've already covered most of the parts needed to make this happen. We know that a change to the `Service Worker` will cause it to spin up a new instance and that change can just be renaming the cache from `v1` to `v2`.

But we haven't covered how to get rid of the old cache. The first piece of the puzzle is the `activate` event which fires when the `Service Worker` becomes active:

```js
self.addEventListener('activate', function(event) {
  // ...
});
```

This event is triggered when the `Service Worker` is ready to control pages, which makes it the perfect time to remove old caches. Like the `install` event, you can use `event.waitUntil` to signal the length of the process. 

While you're activating, the browser will queue other `Service Worker` events such as `fetch`. By the time your `Service Worker` receives its first `fetch`, you know you have the caches how you want them.

You can delete caches using `caches.delete`, passing in the name of the cache.

```js
caches.delete(cacheName);
```

You can also get the names of all your caches using `caches.keys`.

```js
caches.keys();
```

Both of these methods return `Promise`s.

- - -

Next: [Quiz: Update Your CSS](./20-quiz-update-css.md)