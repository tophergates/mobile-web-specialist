# 03.02: QUIZ: SCOPING QUIZ
Given this registration code, which page URLs will this `Service Worker` control?

```js
navigator.serviceWorker.register('/sw.js', {
  scope: '/foo/'
});
```

  - [ ] /
  - [ ] /sw.js
  - [ ] /foo
  - [ ] /foo.html
  - [ ] /foo/
  - [ ] /foo/bar/index.html
  - [ ] /foo/bar

<details>
  <summary>ANSWER</summary>
  <ul>
    <li>/foo/</li>
    <li>/foo/bar/index.html</li>
    <li>/foo/bar</li>
  </ul>
  <p>
  
    The `Service Worker` will control any page URL that begins with the scope. Remember, the trailing slash is important!
    
  </p>
</details>

- - -

Next: [Adding a Service Worker To the Project](./03-adding-service-worker.md)