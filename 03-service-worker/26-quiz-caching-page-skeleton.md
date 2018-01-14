# 03.26: QUIZ: CACHING THE PAGE SKELETON
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-page-skeleton
```

Once the task is complete, enter the Task ID: **serve-skeleton**

## CODING TASK
You only need to edit the Service Worker to complete this task, so head over to `public/js/sw/index.js`.

The root page (`/`) is currently being cached and served in the fetch event. Your job is to cache the page `/skeleton` instead and serve that if the root page is requested.

Once you've changed the code, refresh the page. The browser will see the new Service Worker and notify the user. Click the refresh button to update to the latest version.

You can confirm your changes are working by viewing the source on the root page. The source should be minimal. Head to the test page, enter the Task ID and press enter.

<details>
  <summary>SOLUTION</summary>
  <p>
  
  Start by updating `staticCacheName` to `wittr-static-v4`.
  
  Next, inside the `install` event handler, instead of caching `/` inside of `cache.addAll`, cache `/skeleton`:
  
  Finally, the `fetch` event handler looks like:
  
  ```js
  self.addEventListener('fetch', function(event) {
    var requestURL = new URL(event.request.url);
    
    if (requestURL.origin === location.origin) {
      if (requestURL.pathname === '/') {
        event.respondWith(caches.match('/skeleton'));
        return;
      }
    }
    
    event.respondWith(
      caches.match(event.request).then(function(response) {
        return response || fetch(event.request);
      })
    );
  });
  ```
  
  </p>
</details>

- - -

Next: [Introducing the IDB](../04-idb/01-intro-idb.md)