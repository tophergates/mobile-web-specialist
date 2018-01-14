# 03.17: QUIZ: INSTALL AND CACHE
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-install
```

Once the task is complete, enter the Task ID: **install-cached**

## CODING TASK
Take a look at the code located in `public/js/sw/index.js`. There is an array of URLs to cache there. Your task is to cache those URLs in a cache named `wittr-static-v1`.

Remember to have the Chrome developer tools open and use the `Update on reload` option so you only need to refresh once to see changes.

To verify the state of the cache in the Chrome developer tools, click on the `Application` tab and then `Cache Storage`. Hopefully, you will see your cache in there.

<details>
  <summary>SOLUTION</summary>
  <p>
  
  ```js
  self.addEventListener('install', function(event) {
    var urlsToCache = [
      '/',
      'js/main.js',
      'css/main.css',
      'imgs/icon.png',
      'https://fonts.gstatic.com/s/roboto/v15/2UX7WLTfW3W8TclTUvlFyQ.woff',
      'https://fonts.gstatic.com/s/roboto/v15/d-6IYplOFocCacKzxwXSOD8E0i7KZn-EPnyo3HZu7kw.woff'
    ];
    
    event.waitUntil(
      caches.open('wittr-static-v1')
        .then(function(cache) {
          return cache.addAll(urlsToCache);
        })
    );
  });
  ```
    
  </p>
</details>

- - -

Next: [Quiz: Cache Response](./18-quiz-cache-response.md)