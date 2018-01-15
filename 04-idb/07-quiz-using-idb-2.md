# 04.07: QUIZ: USING IDB 2

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-show-stored
```

Once the task is complete, enter the Task ID: **idb-show**

## CODING TASK

At this point, you should have Wittr posts in the database. Now, you're going to get the posts on the screen. First, verify you've got posts in the database with Chrome developer tools. Once you've verified you've got posts in the database, head over to the `public/js/main/IndexController.js` file.

Previously, we were calling the `IndexController._openSocket` method, but now we're calling the `IndexController._showCachedMessages` method and `then` opening the socket. However, the `_showCachedMessages` method isn't fully implemented yet.

Your task is to get all of the Wittr message objects from the database and pass them to the `IndexController._postsView.addPosts` method. Make sure they are in date order, starting with the latest; which may not be the way they come out of the database.

Once you've made changes to your code, you'll need to bump the cach version number in the `Service Worker` so the changes are picked up.

If your database gets into a bad state, you can remove the database and start fresh in one of two ways:

1. Go to the Chrome developer tools, `Application` panel, `Storage` > `IndexedDB`, and select 'wittr'. On the right you will see the button: 'Delete database'; click it.

2. Alternatively, go into the Chrome developer tools `Console`, type `indexedDB.deleteDatabase('wittr')`, and press enter.

Double check that everything is working by heading to the settings page at http://localhost:8889/, change the connection type to 'Offline', type the Task ID into the input field, and press `ENTER`. You should see posts being displayed even when you load the page offline.

<details>
<summary>SOLUTION</summary>
<p>

```js
IndexController.prototype._showCachedMessages = function() {
  var indexController = this;

  return this._dbPromise.then(function(db) {
    // if we're already showing posts, eg shift-refresh
    // or the very first load, there's no point fetching
    // posts from IDB
    if (!db || indexController._postsView.showingPosts()) return;

    // TODO: get all of the wittr message objects from indexeddb,
    // then pass them to:
    // indexController._postsView.addPosts(messages)
    // in order of date, starting with the latest.
    // Remember to return a promise that does all this,
    // so the websocket isn't opened until you're done!
    var index = db
      .transaction('wittrs')
      .objectStore('wittrs')
      .index('by-date');

    return index.getAll().then(function(messages) {
      indexController._postsView.addPosts(messages.reverse());
    });
  });
};
```

</p>
</details>

---

Next: [Quiz: Cleaning IDB](./08-quiz-cleaning-idb.md)
