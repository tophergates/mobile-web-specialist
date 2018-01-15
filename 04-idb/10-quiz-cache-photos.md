# 04.10: QUIZ: CACHE PHOTOS

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-cache-photos
```

Once the task is complete, enter the Task ID: **cache-photos**

## CODING TASK

To get started, head over to `public/js/sw/index.js` and find the `servePhoto` function. Your task is to implement the `servePhoto` function. Your aim is to serve photos from the cache if they're there. Otherwise, get them from the network. But, put them into the cache for the next time. Remember to use `storageUrl` when matching and putting images into the image cache so you only end up with one image in the cache no matter how many different sizes are requested.

Make sure the `Update on reload` is enabled in Chrome developer tools while you are testing your code. You'll know things are working when you see a 'wittr-content-imgs' cache in the Chrome developer tools in the `Application` panel under `Cache Storage` where the URLs are missing the width and the `.jpg` extension at the end.

You should also be able to take the server offline, reload the page, and still get images. To double check everything is working, head to the settings page, type the Task ID into the input field, and press `ENTER`.

<details>
<summary>SOLUTION</summary>
<p>

```js
function servePhoto(request) {
  // Photo urls look like:
  // /photos/9-8028-7527734776-e1d2bda28e-800px.jpg
  // But storageUrl has the -800px.jpg bit missing.
  // Use this url to store & match the image in the cache.
  // This means you only store one copy of each photo.
  var storageUrl = request.url.replace(/-\d+px\.jpg$/, '');

  // TODO: return images from the "wittr-content-imgs" cache
  // if they're in there. Otherwise, fetch the images from
  // the network, put them into the cache, and send it back
  // to the browser.
  //
  // HINT: cache.put supports a plain url as the first parameter
  return caches.open(contentImgsCache).then(function(cache) {
    return cache.match(storageUrl).then(function(response) {
      return (
        response ||
        fetch(request).then(function(networkResponse) {
          cache.put(storageUrl, networkResponse.clone());
          return networkResponse;
        })
      );
    });
  });
}
```

</p>
</details>

---

Next: [Cleaning Photo Cache](./11-cleaning-photo-cache.md)
