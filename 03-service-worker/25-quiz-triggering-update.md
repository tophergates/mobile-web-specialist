# 03.25: QUIZ: TRIGGERING AN UPDATE
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-update-notify
```

Once the task is complete, enter the Task ID: **update-reload**

## CODING TASK
First take a look in `public/js/main/IndexController.js`. The `_updateReady` method of the `IndexController` is being called whenever there is an update ready to show. We implemented that in the last task, but we're now passing the Service Worker into the method.

In `_updateReady`, the new version message is shown and then if the user clicks the 'refresh' button, you will need to tell the new Service Worker to tell it to take over control of pages immediately. You'll need to handle this message in the Service Worker.

Over in `public/js/sw/index.js`, at the bottom there is a TODO. Here is where you can listen for the message to take over page control.

Back in `public/js/main/IndexController.js`, there is a TODO in the `_registerServiceWorker` method of the `IndexController`. You need to listen for the pages controlling Service Worker changing and using that as a signal to reload the page.

Once you've finished, you need to get those changes picked up by the browser by deleting the existing Service Worker and refresh the page. Next, make a change to your Service Worker and then refresh the page.

Once you've got it working, head over to the settings page and put in the Task ID and press enter. You have 8-seconds to hit the refresh button in the notification. This will confirm it's all working.

<details>
  <summary>SOLUTION</summary>
  <p>
  
  Inside the `public/js/main/IndexControler.js` file, the `_updateReady` method should look like this:
  
  ```js
  IndexController.prototype._updateReady = function(worker) {
    var toast = this._toastsView.show("New version available", {
      buttons: ['refresh', 'dismiss']
    });

    toast.answer.then(function(answer) {
      if (answer != 'refresh') return;
      // TODO: tell the service worker to skipWaiting
      worker.postMessage({
        action: 'skipWaiting'
      });
    });
  };
  ```
  
  Inside the `public/js/sw/index.js` file:
  
  ```js
  // TODO: listen for the "message" event, and call
  // skipWaiting if you get the appropriate message
  self.addEventListener('message', function(event) {
    if (event.data.action === 'skipWaiting') {
      self.skipWaiting();
    }
  });
  ```
  
  Back inside the `IndexController.js` file, at the end of the `_registerServiceWorker` method:
  
  ```js
  navigator.serviceWorker.addEventListener('controllerchange', function(){
    window.location.reload();
  });
  ```
  
  </p>
</details>

- - -

Next: [Quiz: Caching the Page Skeleton](./26-quiz-caching-page-skeleton.md)