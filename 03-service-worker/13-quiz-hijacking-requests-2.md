# 03.13: QUIZ: HIJACKING REQUESTS 2
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout gif-response
```

Once the task is complete, enter the Task ID: **gif-response**

## CODING TASK
Take a look at the code located in `public/js/sw/index.js`. As you can see, your task is to only respond with a `.gif` if the request URL ends with `.jpg`. How you determine that is up to you, but remember that `event.request` gives you information about the request.

<details>
  <summary>SOLUTION</summary>
  <p>
    ```js
    self.addEventListener('fetch', function(event) {
      if (event.request.url.endsWith('.jpg')) {
        event.respondWith(
          fetch('/imgs/dr-evil.gif')
        )
      }
    })
    ```
  </p>
</details>

- - -

Next: [Hijacking Requests 3](./14-hijacking-requests-3.md)