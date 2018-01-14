# 03.03: ADDING A SERVICE WORKER TO THE PROJECT
The project already contains an empty `Service Worker` script file at the following file path:

`/public/js/sw/index.js`

As we discussed before, the `Service Worker` receives events. Let's listen for `fetch` events:

```js
self.addEventListener('fetch', function(event) {
  console.log(event.request);
});
```

When the user navigates to a page within your `Service Worker`s scope, the `Service Worker` controls it. The network request for its HTML goes to the `Service Worker` and triggers a `fetch` event. Not only that, you also get a `fetch` event for every request triggered by that page! CSS, JavaScript, images, even requests to another origin - you get a `fetch` request for each.

- - -

Next: [Quiz: Registering a Service Worker](./04-quiz-registering-service-worker.md)