# 03.11: QUIZ: HIJACKING REQUESTS 1
First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-custom-response
```

Once the task is complete, enter the Task ID: **html-response**

## CODING TASK
Over in the Service Worker script file, located at `public/js/sw/index.js`, alter the Service Worker so it responds with some HTML. It can be whatever HTML you want as long as it includes the class name, `a-winner-is-me`. Make sure the `force update on page load` option is selected in the Chrome developer tools on the `Application` tab so you can see your changes to the Service Worker upon page refresh.

<details>
  <summary>SOLUTION</summary>
  <p>
    &nbsp;
    ```js
    self.addEventListener('fetch', function(event) {
      event.respondWith(
        new Reponse('<p class="a-winner-is-me">Responding from the Service Worker with HTML.</p>', {
          headers: {
            'Content-Type': 'text/html'
          }
        })
      )
    })
    ```
  </p>
</details>

- - -

Next: [Hijacking Requests 2](./12/hijacking-requests-2.md)