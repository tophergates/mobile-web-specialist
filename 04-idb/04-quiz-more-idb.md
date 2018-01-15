# 04.04: QUIZ: MORE IDB

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout task-idb-people
```

Once the task is complete, enter the Task ID: **idb-age**

## CODING TASK

Once again, we're working in the `IDB` script for this exercise, so open the file path `public/js/idb-test/index.js`.

You just learned how to create an `index` to sort people by favoriteAnimal. Your task is to create an `index` where people are ordered by age.

Once you've changed the code, refresh the page and check the Chrome developer tools to see if your favorite animal has appeared.

Double check that everything is working by heading to the settings page at http://localhost:8889/, type the Task ID into the input field, and press `ENTER`.

<details>
<summary>SOLUTION</summary>
<p>

```js
var dbPromise = idb.open('test-db', 4, function(upgradeDb) {
  switch (upgradeDb.oldVersion) {
    case 0:
      var keyValStore = upgradeDb.createObjectStore('keyval');
      keyValStore.put('world', 'hello');
    case 1:
      upgradeDb.createObjectStore('people', { keyPath: 'name' });
    case 2:
      var peopleStore = upgradeDb.transaction.objectStore('people');
      peopleStore.createIndex('animal', 'favoriteAnimal');
    case 3:
      peopleStore = upgradeDb.transaction.objectStore('people');
      peopleStore.createIndex('age', 'age');
  }
});

// ... more code

dbPromise
  .then(function(db) {
    var tx = db.transaction('people');
    var peopleStore = tx.objectStore('people');
    var ageIndex = peopleStore.index('age');

    return ageIndex.getAll();
  })
  .then(function(people) {
    console.log('People by age:', people);
  });
```

</p>
</details>

## CRASH COURSE WRAP-UP

At the moment, we're getting all the items out of the store. But we can actually go through them one at a time using `cursor`s. Taking the age code from the solution, rather than calling `getAll` you can call `openCursor`. This returns a `Promise` for a cursor object representing the first item in the `index`, or `undefined` if there isn't one.

```js
dbPromise
  .then(function(db) {
    var tx = db.transaction('people');
    var peopleStore = tx.objectStore('people');
    var ageIndex = peopleStore.index('age');

    return ageIndex.openCursor();
  })
  .then(function(cursor) {
    if (!cursor) return;
    console.log('Cursored at:', cursor.value.name);
    return cursor.continue();
  });
```

The first person in the `index` is in `cursor.value`. To move on to the next person, we'll call `cursor.continue` which returns a `Promise` for a `cursor` representing the next person, or `undefined` if there isn't one.

A neat trick is name the callback function and then call that function once `cursor.continue` resolves. This sets up a kind of asynchronous loop until `cursor` is `undefined`. Meaning, we're at the end of the list:

```js
dbPromise
  .then(function(db) {
    var tx = db.transaction('people');
    var peopleStore = tx.objectStore('people');
    var ageIndex = peopleStore.index('age');

    return ageIndex.openCursor();
  })
  .then(function logPerson(cursor) {
    if (!cursor) return;
    console.log('Cursored at:', cursor.value.name);
    return cursor.continue().then(logPerson);
  })
  .then(function() {
    console.log('Done cursoring');
  });
```

So when we hit this next step in the `Promise` chain, we'll have gone through the whole object store. At the moment, this is just a complicated way of calling `getAll`, but `cursor`s become really useful when you want to modify items as you're looping through them.

You can use `cursor.update` to change the value, or `cursor.delete` to remove it. You could also skip some items using `cursor.advance`:

```js
dbPromise
  .then(function(db) {
    var tx = db.transaction('people');
    var peopleStore = tx.objectStore('people');
    var ageIndex = peopleStore.index('age');

    return ageIndex.openCursor();
  })
  .then(function(cursor) {
    if (!cursor) return;
    return cursor.advance(2);
  })
  .then(function logPerson(cursor) {
    if (!cursor) return;
    console.log('Cursored at:', cursor.value.name);
    return cursor.continue().then(logPerson);
  })
  .then(function() {
    console.log('Done cursoring');
  });
```

Notice that the number 2 is passed to `cursor.advance` here, this causes the first two items to be skipped.

That covers a majority of the `IndexedDB API`. In the next chapter, we'll put some of that knowledge into practice on Wittr itself.

---

Next: [Using the IDB Cache and Display Entries](./05-idb-cache-display-entries.md)
