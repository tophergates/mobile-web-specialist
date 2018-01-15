# 04.06: QUIZ: USING THE IDB CACHE

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-idb-store
```

Once the task is complete, enter the Task ID: **idb-store**

## CODING TASK

We're back to editing Wittr, so head over to `public/js/main/IndexController.js`.

In the constructor, we're creating a `Promise` for a database by calling the `openDatabase` function. This function is incomplete.

Your task is to return a `Promise` for a database called 'wittr' that has an object store called 'wittrs' that uses 'id' as its key and has an `index` called called 'by-date', which is sorted by the 'time' property.

Once you've done that, you'll need to add messages to the database. Down in the `IndexController._onSocketMessage` method, the database has been fetched. Your task is to add each of the messages to the Wittr store. Note that we're not using the entries in the database yet - we'll do that in the next chapter.

This is a lot to cover. If you get stuck, refer to the notes from the previous sections or take a look at the code in `public/js/idb-test/index.js` for reference.

If your database gets into a bad state, you can remove the database and start fresh in one of two ways:

1. Go to the Chrome developer tools, `Application` panel, `Storage` > `IndexedDB`, and select 'wittr'. On the right you will see the button: 'Delete database'; click it.

2. Alternatively, go into the Chrome developer tools `Console`, type `indexedDB.deleteDatabase('wittr')`, and press enter.

Once you've created the database, refresh Wittr in the browser to run your code. Make sure you have `Update on reload` selected in the `Application` panel of the Chrome developer tools so you only need to refresh once to see changes.

Double check that everything is working by heading to the settings page at http://localhost:8889/, type the Task ID into the input field, and press `ENTER`.

<details>
<summary>SOLUTION</summary>
<p>

```js
// Open the database
function openDatabase() {
  // If the browser doesn't support service worker,
  // we don't care about having a database
  if (!navigator.serviceWorker) {
    return Promise.resolve();
  }

  // TODO: return a promise for a database called 'wittr'
  // that contains one objectStore: 'wittrs'
  // that uses 'id' as its key
  // and has an index called 'by-date', which is sorted
  // by the 'time' property
  return idb.open('wittr', 1, function(upgradeDb) {
    var store = upgradeDb.createObjectStore('wittrs', {
      keyPath: 'id'
    });

    store.createIndex('by-date', 'time');
  });
}

// ... more code

// called when the web socket sends message data
IndexController.prototype._onSocketMessage = function(data) {
  var messages = JSON.parse(data);

  this._dbPromise.then(function(db) {
    if (!db) return;

    // TODO: put each message into the 'wittrs'
    // object store.
    var tx = db.transaction('wittrs', 'readwrite');
    var store = tx.objectStore('wittrs');

    messages.forEach(function(message) {
      store.put(message);
    });

    return tx.complete;
  });

  this._postsView.addPosts(messages);
};
```

</p>
</details>

---

Next: [Quiz: Using IDB 2](./07-quiz-using-idb-2.md)
