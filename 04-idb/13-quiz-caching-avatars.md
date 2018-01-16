# 04.13: QUIZ: CACHING AVATARS

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-cache-avatars
```

Once the task is complete, enter the Task ID: **cache-avatars**

## CODING TASK

Compared to what you've done so far, this last part should be a breeze! We just need to cache the avatar images. This is almost the same as caching the photo images, but there's one small difference...

Some people like to change their avatar a lot and we don't want people to be stuck with some old version of their avatar. So, when we fetch a particular avatar from the cache we will also fetch it from the network and update the cache.

Avatars are also responsive images, but they vary by density rather than width. Pretty much the same as photo images, but a slightly different URL pattern:

```html
<img width="40" height="40"
  src="/avatars/sam-1x.jpg"
  srcset="/avatars/sam-2x.jpg 2x,
          /avatars/sam-3x.jpb 3x">
```

We're going to cache the avatars in the same cache as photos. Notice that the `imagesNeeded` `array` in the updated code branch now includes the avatar image URLs so that it includes them in the images we want to keep. We don't want those getting lost in the cleanup.

Head over to `public/js/sw/index.js`. There are two things for you to implement:

1. You need to call the `serveAvatar` function from the `fetch` event handler.

2. Then, you'll need to implement the `serveAvatar` function. The function should return avatar images from the cache if they're there. If not, get it from the network and put it in the cache. But here's the difference, even if you return it from the cache you'll need to go to the network to update it for the next `fetch`. Once again, we're removing the size-specific parts of the URL with the `storageUrl` variable. So use this URL to `put` and `match` in the cache. The solution will be similar to `servePhoto`, but not exactly the same.

Once you've written the code, head over to the browser and enable `Update on reload` in Chrome developer tools. You should see avatar images arriving into the cache. If you go 'Offline' and refresh, the avatar images should still load.

To double check everything is working, head to the settings page and type the Task ID into the input field and press `ENTER`.

<details>
<summary>SOLUTION</summary>
<p>

Inside of the `fetch` event handler:

```js
// TODO: respond to avatar urls by responding with
// the return value of serveAvatar(event.request)
if (requestUrl.pathname.startsWith('/avatars/')) {
  event.respondWith(serveAvatar(event.request));
  return;
}
```

The `serveAvatar` function:

```js
function serveAvatar(request) {
  // Avatar urls look like:
  // avatars/sam-2x.jpg
  // But storageUrl has the -2x.jpg bit missing.
  // Use this url to store & match the image in the cache.
  // This means you only store one copy of each avatar.
  var storageUrl = request.url.replace(/-\dx\.jpg$/, '');

  // TODO: return images from the "wittr-content-imgs" cache
  // if they're in there. But afterwards, go to the network
  // to update the entry in the cache.
  //
  // Note that this is slightly different to servePhoto!
  return caches.open(contentImgsCache).then(function(cache) {
    return cache.match(storageUrl).then(function(response) {
      var fetchPromise = fetch(request).then(function(networkResponse) {
        cache.put(storageUrl, networkResponse.clone());
        return networkResponse;
      });

      return response || fetchPromise;
    });
  });
}
```

</p>
</details>

---

Next: [Outro](./14-outro.md)
