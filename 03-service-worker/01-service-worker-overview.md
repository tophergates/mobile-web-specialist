# 03.01: AN OVERVIEW OF SERVICE WORKER
A service worker is a JavaScript file that sits between you and network requests. It's a type of web worker, meaning it runs separately from the page. It isn't visible to the user and cannot access the DOM. However, it does control pages.

By control I mean, intercepts requests as the browser makes them. From there you can do, whatever you want really.

## REGISTERING A SERVICE WORKER

```js
navigator.serviceWorker.register('/sw.js')
```

The register method on the serviceWorker object in the navigator object returns a `Promise` so you can then use callbacks for success and failure of registering the Service Worker.

```js
navigator.serviceWorker.register('/sw.js')
  .then(function(registration) {
    console.log('ServiceWorker registered:', registration);
  })
  .catch(function(error) {
    console.error('ServiceWorker registration failed:', error);
  })
```

If you call register when the Service Worker is already registered, that is fine. The browser will not re-register it, it will return a promise for the existing registration.

## SERVICE WORKER SCOPE

You can also provide a scope. The Service Worker will control any page whose URL begins with the scope and ignore any that don't.

```js
navigator.serviceWorker.register('/sw.js', {
  scope: '/my-app/'
})
```

In this example, the Service Worker will control pages at:

  - [x] /my-app/
  - [x] /my-app/hello/world/

But not at:

  - [ ] /
  - [ ] /another-app/
  - [ ] /my-app

You can have multiple Service Workers with different scopes. Scopes let you have a different Service Worker for each project. 

**Default scope is determined by the location of the Service Worker script.**

| SW URL   | Default Scope |
| -------- | ------------- |
| /foo/sw.js | /foo/       |
| /foo/bar/sw.js | /foo/bar/ |
| /sw.js   | / |

Usually you don't need to set the scope, just put the Service Work script in the right place.

## WHAT HAPPENS WITHIN THE SERVICE WORKER?

Well, you listen for particular events. Just like in other JavaScript, you listen to them and react to them - even prevent the default if you want:

```js
  self.addEventListener('install', function(event) {
    // ...
  });
  
  self.addEventListener('activate', function(event) {
    // ...
  });
  
  self.addEventListener('fetch', function(event) {
    // ...
  })
```

## WHAT ABOUT BROWSER SUPPORT?
Check to see if Service Workers are available on specific browsers at https://jakearchibald.github.io/isserviceworkerready/

Just because technology isn't universally supported, doesn't mean you should wait. The Service Worker is entirely progressive enhancement friendly, meaning we can use it in browsers that support it without disrupting users of older browsers. In fact, using this stuff today to improve the user experience will encourage other browsers to implement it so their users get the benefit too.

To use Service Workers in a safe, progressive enhacing way, just make sure you wrap code in a simple feature detect.

```js
if (navigator.serviceWorker) {
  navigator.serviceWorker.register('/sw.js');
}

// or

if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js');
}
```

- - -

Next: [Quiz: Scoping Quiz](./02-quiz-scoping.md)