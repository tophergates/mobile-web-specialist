# 04.12: QUIZ: CLEANING PHOTO CACHE

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-clean-photos
```

Once the task is complete, enter the Task ID: **cache-clean**

## CODING TASK

Head over to `public/js/main/IndexController.js`. Your task is to implement the `IndexController._cleanImageCache` method. It involves getting all the messages from the database, looking at what photos they need, then going through the images cache and getting rid of the ones that aren't needed anymore. **Remember that the 'photo' properties value may not exactly match the URL in the cache.**

When you're finished, turn `Update on reload` off in the Chrome developer tools, enter the Task ID in the settings page, and refresh the Wittr page within 8 seconds.

<details>
<summary>SOLUTION</summary>
<p>

```js
IndexController.prototype._cleanImageCache = function() {
  return this._dbPromise.then(function(db) {
    if (!db) return;

    // TODO: open the 'wittr' object store, get all the messages,
    // gather all the photo urls.
    //
    // Open the 'wittr-content-imgs' cache, and delete any entry
    // that you no longer need.
    var imagesNeeded = [];

    var tx = db.transaction('wittrs');
    return tx
      .objectStore('wittrs')
      .getAll()
      .then(function(messages) {
        messages.forEach(function(message) {
          if (message.photo) {
            imagesNeeded.push(message.photo);
          }
        });

        return caches.open('wittr-content-imgs');
      })
      .then(function(cache) {
        return cache.keys().then(function(requests) {
          requests.forEach(function(request) {
            var url = new URL(request.url);

            if (!imagesNeeded.includes(url.pathname)) {
              cache.delete(request);
            }
          });
        });
      });
  });
};
```

</p>
</details>

---

Next: [Quiz: Caching Avatars](./13-quiz-caching-avatars.md)
