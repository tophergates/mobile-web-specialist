# 04.05: USING THE IDB CACHE AND DISPLAY ENTRIES

The plan here is to create a database for Wittr that stores the posts. When Wittr loads, via a `Service Worker`, it does so without going to the network. It fetches the page skeleton and assets straight from the cache.

At the moment, we have to go to the network for posts. We're going to change that so instead we'll get posts from the database and display them. Meaning we'll show post content before we go to the network.

Next, we'll connect the `WebSocket` to get updated posts. `WebSocket`s bypass both the `Service Worker` and the HTTP cache. As new posts arrive, we'll ad them to the database for next time.

## POPULATE THE DATABASE

The first thing we'll do is populate the database. We'll deal with displaying its contents later. If you take a look at the file path: `public/js/main/IndexController.js`, you can see that the `IndexController._openSocket` method is called to open the `Web Socket`. In that method you can see a listener for the `message` event, which hands off to the `IndexController._onSocketMessage` method passing in the data it received.

`IndexController._onSocketMessage` then parses the JSON data and passes it to `IndexController._postsView.addPosts`.

The data is an `array` of `objects`; each `object` in the `array` has the following shape:

```js
{
  avatar: String,
  body: String,
  id: String,
  name: String,
  time: String
}
```

The goal here is to store these objects straight into `IndexedDB`. The obvious primary key here is the 'id' property. And we'll want to be able to display the posts in date order, so we'll need to be able to create an `index` on the 'time' property.

---

Next: [Quiz: Using IDB Cache](./06-quiz-idb-cache.md)
