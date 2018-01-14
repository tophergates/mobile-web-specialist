# 03.20: QUIZ: UPDATE YOUR CSS
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-handling-updates
```

Once the task is complete, enter the Task ID: **new-cache-ready**

## CODING TASK
This time, make sure the `Update on reload` option is disabled in Chrome developer tools.

Take a look at the code located in `public/js/sw/index.js`. Your task is to change the theme of the site. Thankfully, this is pretty easy as all the colors are stored SCSS in variables.

You can see those variables in `public/scss/_theme.scss`. You can either uncomment the green and pink theme, or make one of your own up. The only requirement is that the primary color changes so that the site's header changes color.

Then, in the `Service Worker`, update the cache name and use the `activate` event to remove the old cache. Once you've done that, reload the page to see it working.

In the Chrome developer tools in the `Application` tab, you should see a `Service Worker` in the waiting position. **Don't let it activate yet!** Instead, head over to the test page and enter the Task ID to confirm your solution is working.

<details>
  <summary>SOLUTION</summary>
  <p>
  
  ```js
  var staticCacheName = 'wittr-static-v2';
  
  self.addEventListener('install', function(event) {
    event.waitUntil(
      caches.open(staticCacheName)
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
  
  // The easy way:
  // self.addEventListener('activate', function(event) {
  //   event.waitUntil(
  //     caches.delete('wittr-static-v1')
  //   );
  // });
  
  // The scalable and safer way:
  self.addEventListener('activate', function(event) {
    event.waitUntil(
      caches.keys()
        .then(function(cacheNames) {
          return Promise.all(
            cacheNames.filter(function(cacheName) {
              return cachName.startsWith('witter-') && cacheName !== staticCacheName;
            }).map(function(cacheName) {
              return caches.delete(cacheName);
            })
          );
        })
    );
  })
  
  self.addEventListener('fetch', function(event) {
    event.respondWith(
      caches.match(event.request)
        .then(function(response) {
          return response || fetch(event.request);
        })
    );
  });
  ```
    
  First we get all the keys of any existing caches using `caches.keys`. This returns a `Promise` with that resolves to an `Array` of cache names. We return `Promise.all` which takes an `Array` of `Promise`s and waits for all of those `Promise`s to resolve.
    
  We then `filter` the `Array` of cache names; remember, we only care about caches that start with `wittr-` and do not equal the `staticCacheName` (the name of our current cache).
    
  Finally, we `map` over the filtered `Array` and delete each of those caches with `caches.delete`.
  
  </p>
</details>

- - -

Next: [Quiz: Update Your CSS 2](./21-quiz-update-css-2.md)