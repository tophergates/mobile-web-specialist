# 03.15: QUIZ: HIJACKING REQUESTS 3
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout error-handling
```

Once the task is complete, enter the Task ID: **gif-404**

## CODING TASK
Take a look at the code located in `public/js/sw/index.js`. Rather than responding with custom text, your task is to respond with the Dr. Evil `.gif`. This involves fetching the `.gif` from the network, which was covered in the last section.

**NOTE: This quiz indicates that you should see a cat gif if you completed it successfully. However, the gif was updated and it is no longer a cat.**

<details>
  <summary>SOLUTION</summary>
  <p>
  
  ```js
  self.addEventListener('fetch', function(event) {
    event.respondWith(
      fetch(event.request)
        .then(function(response) {
          if(response.status === 404) {
            return fetch('/imgs/dr-evil.gif');
          }
          return response;
        })
        .catch(function() {
          return new Response("Uh oh, that totally failed!");
        })
    );
  });
  ```
  
  </p>
</details>

- - -

Next: [Caching and Serving Assets](./16-caching-serving-assets.md)