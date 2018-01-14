# 03.22: QUIZ: ADDING UX
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-update-notify
```

Once the task is complete, enter the Task ID: **update-notify**

## CODING TASK
We're going to edit a different file this time: in `public/js/main/IndexController.js`. You might remember this file from earlier in the course when you registered a `Service Worker`.

There's a new method here: `_updateReady`. Calling this will show a notification to the user. Your job is to call it at the correct time(s).

There is a series of comments to guide you along the way. Once you've coded it up, you'll need to get those changes picked up by the browser. The easiest way to accomplish this is to delete the `Service Worker`, then refresh the page. This will refetch and cache your JavaScript.

Now, make a change to your `Service Worker`. Adding a simple comment will suffice. Then refresh the page once more.

Once you've got the notification working, head over to the settings page and type the Task ID to verify your solution is correct.

<details>
  <summary>SOLUTION</summary>
  <p>
  
  Inside the `IndexController.js` file:
  
  ```js
  IndexController.prototype._registerServiceWorker = function() {
    if (!navigator.serviceWorker) return;
    
    var indexController = this;
    
    navigator.serviceWorker.register('sw.js').then(function(reg) {
      // TODO: if there's no controller, this page wasn't loaded
      // via a service worker, so they're looking at the latest version.
      // In that case, exit early
      if (!navigator.serviceWorker.controller) {
        return;
      }
      
      // TODO: if there's an updated worker already waiting, call
      // indexController._updateReady()
      if (reg.waiting) {
        indexController._updateReady();
        return;
      }
      
      // TODO: if there's an updated worker installing, track its
      // progress. If it becomes "installed", call
      // indexController._updateReady()
      if (reg.installing) {
        indexController._trackInstalling(reg.installing);
        return;
      }
      
      // TODO: otherwise, listen for new installing workers arriving.
      // If one arrives, track its progress.
      // If it becomes "installed", call
      // indexController._updateReady()
      reg.addEventListener('updatefound', function() {
        indexController._trackInstalling(reg.installing);
        return;
      })
    });
  };
  
  IndexController.prototype._trackInstalling = function(sw) {
    var indexController = this;
    
    sw.addEventListener('statechange', function() {
      if (sw.state === 'installed') {
        indexController._updateReady();
      }
    });
  };
  ```
  
  </p>
</details>

- - -

Next: [Triggering an Update](./24-triggering-update.md)