# 04.08: QUIZ: CLEANING IDB

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-clean-db
```

Once the task is complete, enter the Task ID: **idb-clean**

## CODING TASK

Things are looking great, but at the moment we're only adding things to the database. At some point, you'll end up with thousands and thousands of items in there. And the browser's going to not allow any more storage. So we need to make sure we're only keeping what we need.

Head over to the `public/js/main/IndexController.js` file and take a look at the `IndexController._onSocketMessage` method. We're already adding items into the database, but after that you need to ensure there are no more than 30 items in the object store. The code in `public/js/idb-test/index.js` should act as a good reference to achieve this task.

Again, if your database gets into a bad state, you can remove the database and start fresh in one of two ways:

1. Go to the Chrome developer tools, `Application` panel, `Storage` > `IndexedDB`, and select 'wittr'. On the right you will see the button: 'Delete database'; click it.

2. Alternatively, go into the Chrome developer tools `Console`, type `indexedDB.deleteDatabase('wittr')`, and press enter.

Once you've put the code in place, refresh Wittr in the browser to run your code. I recommend `SHIFT` refreshing to bypass the `Service Worker` until you've got everything working. Then, bump the version of the `Service Worker` static cache to pick up your changes.

If everything's working, you should see the 'wittr' database in Chrome developer tools and in there, you should see no more than 30 messages. To double check that everything is working: Head to the settings page at http://localhost:8889/, type the Task ID into the input field, and press `ENTER`.

<details>
<summary>SOLUTION</summary>
<p>

```js
IndexController.prototype._onSocketMessage = function(data) {
  var messages = JSON.parse(data);

  this._dbPromise.then(function(db) {
    if (!db) return;

    var store = db.transaction('wittrs', 'readwrite').objectStore('wittrs');
    var index = store.index('by-date');

    messages.forEach(function(message) {
      store.put(message);
    });

    // TODO: keep the newest 30 entries in 'wittrs',
    // but delete the rest.
    //
    // Hint: you can use .openCursor(null, 'prev') to
    // open a cursor that goes through an index/store
    // backwards.
    return index
      .openCursor(null, 'prev')
      .then(function(cursor) {
        return cursor.advance(30);
      })
      .then(function deletePost(cursor) {
        if (!cursor) return;
        cursor.delete();
        return cursor.continue().then(deletePost);
      });
  });

  this._postsView.addPosts(messages);
};
```

</p>
</details>

---

Next: [Cache Photos](./09-cache-photos.md)
