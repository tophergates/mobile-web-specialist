# 03.04: QUIZ: REGISTERING A SERVICE WORKER
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-register-sw
```

Once the task is complete, enter the Task ID: **registered**

## CODING TASK
Register the Service Worker

<details>
  <summary>SOLUTION</summary>
  <p>
  Inside of the file ```public/js/main/IndexController.js``` locate the ```IndexController.prototype._registerServiceWorker``` function and enter the following inside the function body:
  
  ```js
  if (!navigator.serviceWorker) return;
  
  navigator.serviceWorker.register('/sw.js')
    .then(function(registration) {
      console.log('Service Worker registered:', registration);
    })
    .catch(function(error) {
      console.error('Service Worker could not be registered:', error);
    });
  ```
  
  Refresh the page once after completing your code to register the Service Worker. Refresh the page a second time to begin seeing console.log messages from the Service Worker fetch event.
  </p>
</details>

- - -

Next: [The Service Worker Lifecycle](./05-service-worker-lifecycle.md)