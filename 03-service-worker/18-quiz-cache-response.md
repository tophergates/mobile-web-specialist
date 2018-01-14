# 03.18: QUIZ: CACHE RESPONSE
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-cache-response
```

Once the task is complete, enter the Task ID: **cache-served**

## CODING TASK
Take a look at the code located in `public/js/sw/index.js`. Your task is to respond to a fetch event with an entry from the cache if there is one. If there isn't, fetch from the network.

<details>
  <summary>SOLUTION</summary>
  <p>
    ```js
    self.addEventListener('install', function(event) {
      event.waitUntil(
        caches.open('wittr-static-v1')
          .then(function(cache) {
            return cache.addAll([
              '/',
              'js/main.js',
              'css/main.css',
              'imgs/icon.png',
              'https://fonts.gstatic.com/s/roboto/v15/2UX7WLTfW3W8TclTUvlFyQ.woff',
              'https://fonts.gstatic.com/s/roboto/v15/d-6IYplOFocCacKzxwXSOD8E0i7KZn-EPnyo3HZu7kw.woff'
            ]);
          })
      );
    });
    
    self.addEventListener('fetch', function(event) {
      event.respondWith(
        caches.match(event.request)
          .then(function(response) {
            return response || fetch(event.request);
          })
      );
    });
    ```
  </p>
</details>

- - -

Next: [Updating the Static Cache](./19-updating-static-cache.md)