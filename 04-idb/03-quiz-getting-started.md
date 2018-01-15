# 04.03: QUIZ: GETTING STARTED WITH IDB

First get the project into the sample state by using the following commands:

```shell
$ git reset --hard
$ git checkout page-skeleton
```

Once the task is complete, enter the Task ID: **idb-animal**

## CODING TASK

We're working in a different script file for this exercise, so open the file path `public/js/idb-test/index.js`. Here, you can see all the code that Jake was writing. To see the code working in the browser, open: http://localhost:8888/idb-test/

You get a blank screen, but a few messages in the `Console`. Also, you can see the database appearing in the Chrome developer tools. You've already seen how to add and remove items from the 'keyval' object store.

Your task is to set the 'favoriteAnimal' key to the name of your favorite animal, whatever that is. Once you've changed the code, refresh the page and check the Chrome developer tools to see if your favorite animal has appeared in `IDB`.

Double check that everything is working by heading to the settings page at http://localhost:8889/, type the Task ID into the input field, and press `ENTER`.

<details>
<summary>SOLUTION</summary>
<p>

```js
dbPromise
  .then(function(db) {
    var tx = db.transaction('keyval', 'readwrite');
    var keyValStore = tx.objectStore('keyval');
    keyValStore.put('X', 'favoriteAnimal');
    return tx.complete;
  })
  .then(function() {
    console.log('Added favoriteAnimal:X to keyval');
  });
```

</p>
</details>

## DIVE DEEPER

So far we've created a key/val object store, but what if I wanted to created a different store with `object`s all of the same kind: People, for instance? To do that, you'll need to create another object store. As mentioned earlier, to maintain database integrity, you can only creat object stores and indexes within the `upgradeDb` method. But the `upgradeDb` isn't being called, because its already been called for `version` 1 of the database...

To fix this, start by bumping the `version` up to 2. Then, create a new object store called 'people'. The 'people' object store isn't going to have separate keys. Instead, make the 'name' property of the objects inside the store be the key:

```js
var dbPromise = idb.open('test-db', 2, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
  keyValStore.put('world', 'hello');
  upgradeDb.createObjectStore('people', {
    keyPath: 'name'
  });
});
```

In the real world, you can have more than one person with the same name - but for the sake of this demo we're going to assume names are unique.

By doing this, we've actually created a bug here. You can see it if you try reloading the page and taking a look at the `Console`.

Because the browser hasn't seen `version` 2 yet, it will attempt to create a new object store with the name 'keyval' first. And when it does so, it will fail because the 'keyval' store already exists.

Thankfully, the `upgradeDb` object has a property named `oldVersion` which tells us the `version` the browser already knows about. We can use a `switch` statement to make the browser skip the parts it already knows about:

```js
var dbPromise = idb.open('test-db', 2, function(upgradeDb) {
  switch (upgradeDb.oldVersion) {
    case 0:
      var keyValStore = upgradeDb.createObjectStore('keyval');
      keyValStore.put('world', 'hello');
    case 1:
      upgradeDb.createObjectStore('people', {
        keyPath: 'name'
      });
  }
});
```

Usually with `switch` statements, you include a `break` after each case, but we specifically do not want to do that here. So, if the browser hasn't setup a database it will start with `case` 0 and will continue to _fall-through_ the `case`s. Otherwise, if the browser already has `version` 1, it will start with `case` 1 and only setup the new 'person' object store.

If you refresh the browser now, the errors are gone and the store has appeared in the `Application` panel under the `Storage` > `IndexedDB` section.

Now we can create a `transaction` to put data in the new store:

```js
// .. previous code

dbPromise
  .then(function(db) {
    var tx = db.transaction('people', 'readwrite');
    var peopleStore = tx.objectStore('people');

    peopleStore.put({
      name: 'Sam Munoz',
      age: 25,
      favoriteAnimal: 'dog'
    });

    peopleStore.put({
      name: 'Susan Keller',
      age: 34,
      favoriteAnimal: 'cat'
    });

    peopleStore.put({
      name: 'Lillie Wolfe',
      age: 28,
      favoriteAnimal: 'dog'
    });

    peopleStore.put({
      name: 'Marc Stone',
      age: 39,
      favoriteAnimal: 'cat'
    });

    return tx.complete;
  })
  .then(function() {
    console.log('People added!');
  });
```

In this model, a person is just a plain JavaScript `object`. Notice that we're calling `put`, but we aren't providing a key this time. This is because when we initially created the object store for people, we specified the key at that time which told the store to treat the name property as the key.

If you refresh the browser, you can see this operation has completed and the people have been added.

Now, let's read the people in the store. To do that, we create another `transaction` and use the `getAll` method of the object store which returns a `Promise` that resolves to an `array` of all the objects in the store:

```js
dbPromise
  .then(function(db) {
    var tx = db.transaction('people');
    var peopleStore = tx.objectStore('people');
    return peopleStore.getAll();
  })
  .then(function(people) {
    console.log('People:', people);
  });
```

Notice that since the key is 'name', all of the objects will be in alphabetical order based on their name. This presents the next challenge... What if we wanted to group them together based on their favorite animal? This is where `index`es come in.

Like other modifications to the database, `index`es can only be created as part of a `version` upgrade. So we need to bump the version number up and start adding to our `switch` statement:

```js
var dbPromise = idb.open('test-db', 3, function(upgradeDb) {
  switch (upgradeDb.oldVersion) {
    case 0:
      var keyValStore = upgradeDb.createObjectStore('keyval');
      keyValStore.put('world', 'hello');
    case 1:
      upgradeDb.createObjectStore('people', {
        keyPath: 'name'
      });
    case 2:
      var peopleStore = upgradeDb.transaction.objectStore('people');
      peopleStore.createIndex('animal', 'favoriteAnimal');
  }
});
```

In the last `case` block, you can see that we're using the `transaction` object on the `database` object to obtain the 'people' object store, similar to obtaining the object store when creating `transaction`s. Next, we use the `createIndex` method of the object store to create an `index` named 'animal' from the 'favoriteAnimal' property.

To use this index, we will modify the code where we are listing all the people. We'll start by getting the `index` from the object store by it's name: 'animal'.

An `index` has a very similar API to the object store. So, instead of calling `getAll` on the object store, we can call it on the `index`:

```js
dbPromise
  .then(function(db) {
    var tx = db.transaction('people');
    var peopleStore = tx.objectStore('people');
    var animalIndex = peopleStore.index('animal');

    return animalIndex.getAll();
  })
  .then(function(people) {
    console.log('People:', people);
  });
```

If you refresh the browser, this time the people are sorted by favoriteAnimal. In Chrome developer tools, you can see the `index` as well.

You can even execute queries on the `index`. For instance, let's say you just want to see the people whose favoriteAnimal is 'cat'. You simply pass the `string` 'cat' to the `getAll` method:

```js
dbPromise
  .then(function(db) {
    var tx = db.transaction('people');
    var peopleStore = tx.objectStore('people');
    var animalIndex = peopleStore.index('animal');

    return animalIndex.getAll('cat');
  })
  .then(function(people) {
    console.log('People:', people);
  });
```

---

Next: [Quiz: More IDB](./04-quiz-more-idb.md)
