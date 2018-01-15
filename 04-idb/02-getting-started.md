# 04.02: GETTING STARTED WITH IDB
Hold on tight - you're now going to get a crash course in `IndexedDB`... Let's get through this as quickly as possible, and hopefully in one piece.

Start off by opening your browser to http://localhost:8888/idb-test/

You'll see that it is just a blank page with a script tag. This script is located at the file path: `public/js/idb-test/index.js`

All the script contains, at the moment, is an import statement for the `idb` library.

If you open the `idb` libraries examples (https://github.com/jakearchibald/idb), to open a database you can see that the function signature is `idb.open(name, version, upgradeCallback)`.

```js
import idb from 'idb';

idb.open('test-db', 1, function(upgradeDb) {
  
});
```

The `upgradeCallback` function will be called if the browser hasn't heard about this database before, or if the version it knows about is less than the `version` specified. 

You'll notice that the callback function gets the `upgradeDb` parameter, which is used to define the database. To ensure database integrity, this is the only place you can create and remove object stores and indexes.

The API for `upgradeDb` is in the library, but it's mostly just a mirror of the real `IndexedDB API`, except for a few minor differences which we will cover.

## CREATEOBJECTSTORE
The library documentation indicates that `createObjectStore` is the same for the `IndexedDB API`. So, MDN can give us the full detail of the API: https://developer.mozilla.org/en-US/docs/Web/API/IDBDatabase/createObjectStore

In short, the syntax for `createObjectStore` looks something like this:

```js
var objectStore = IDBDatabase.createObjectStore(name, options);
```

The object store name is required, while the options parameter is optional.

So we'll create an object store with a name of 'keyval'. Because we haven't told it otherwise, this store has a key that is separate from the data - which is exactly what we want for a key/value store:

```js
import idb from 'idb';

idb.open('test-db', 1, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
});
```

Now we need to add some content to the object store. We can accomplish this using the `put` method of the object store (`keyValStore`). The `put` method has the following syntax:

```js
var request = objectStore.put(item, key);
```

The `put` method returns a `Promise` that resolves to an `IDBRequest` object. *Note: Whenever the `IDB API` returns an `IDBRequest` object, the `IDB` library will return a `Promise` that resolves with an `IDBRequest` object.*

So let's put `put` to use and create some content in the `keyValStore`:

```js
import idb from 'idb';

idb.open('test-db', 1, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
  keyValStore.put('world', 'hello');
})
```

The database is setup, but we need to add one last piece to this to wrap it up. `idb.open` returns a `Promise` that resolves with an `IDBDatase` object. So we'll store that `Promise` for later:

```js
import idb from 'idb';

var dbPromise = idb.open('test-db', 1, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
  keyValStore.put('world', 'hello');
})
```

Later, we can use the `IDBDatabase` object to `get` and `set` items in the database.

Head back to the browser, refresh the page to run the new code. Then open Chrome developer tools and click the `Application` tab. Under the `Storage` section, click on `IndexedDB`, locate the `keyval` store and click on it. Then, click the button to refresh the database. You should see the key of 'hello' and the value of 'world' inside.

## READING FROM THE DATABASE
If you want to read from the database, you need to create a `transaction`.

You can create a transaction with the `db.transaction` method by passing it the object store name(s) as a `string` or an `array`:

```js
import idb from 'idb';

var dbPromise = idb.open('keyval', 1, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
  keyValStore.put('world', 'hello');
});

dbPromise.then(function(db) {
  var tx = db.transaction('keyval');
});
```

The `db.transaction` method returns an `IDBTransaction` object. You can use the `IDBTransaction` object to call the `objectStore` method, passing in the name of the object store you want. In this case: 'keyval':

```js
import idb from 'idb';

var dbPromise = idb.open('keyval', 1, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
  keyValStore.put('world', 'hello');
});

dbPromise.then(function(db) {
  var tx = db.transaction('keyval');
  var keyValStore = tx.objectStore('keyval');
});
```

The `transaction.objectStore` method returns, you guessed it, an `IDBObjectStore` object. Now that we have the `IDBObjectStore`, we can use the `get` method to return the value of the key we are interested in, 'hello':

```js
import idb from 'idb';

var dbPromise = idb.open('keyval', 1, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
  keyValStore.put('world', 'hello');
});

dbPromise.then(function(db) {
  var tx = db.transaction('keyval');
  var keyValStore = tx.objectStore('keyval');
  return keyValStore.get('hello');
});
```

Since the `get` method returns a `Promise` which resolves to the value we're looking for:

```js
import idb from 'idb';

var dbPromise = idb.open('keyval', 1, function(upgradeDb) {
  var keyValStore = upgradeDb.createObjectStore('keyval');
  keyValStore.put('world', 'hello');
});

dbPromise.then(function(db) {
  var tx = db.transaction('keyval');
  var keyValStore = tx.objectStore('keyval');
  return keyValStore.get('hello');
}).then(function(val) {
  console.log('The value of "hello" is:', val);
});
```

Save the file, refresh your browser and check the `Console` to see the output.

## ADDING ANOTHER VALUE TO THE OBJECT STORE
Let's say you want to add another value to the object store. To do that, you create a `transaction`, just like before, but you specify the `mode` of the `transaction` as `readwrite`.

```js
// ... previous code

dbPromise.then(function(db) {
  var tx = db.transaction('keyval', 'readwrite');
});
```

Now you can get you object stores just as before, but this time call `put` on it to set a value. Let's set the key 'foo' with a value 'bar':

```js
// ... previous code

dbPromise.then(function(db) {
  var tx = db.transaction('keyval', 'readwrite');
  var keyValStore = tx.objectStore('keyval');
  keyValStore.put('bar', 'foo');
});
```

As with the `get` method, the `put` method also returns a `Promise`. But, this `Promise` doesn't necessarily mean the operation worked. **Remember, if the `transaction` fails all the operations that are part of it are undone.** This means that you can do a lot of work in a `transaction` and be sure that it won't be left or accessed in some kind of half-finished state. Either it all happens, or none of it happens.

To check if the `transaction` has completed, we can use `transaction.complete`; which is a `Promise` that fulfills if and when the `transaction` completes, and it rejects if it fails:

```js
// ... previous code

dbPromise.then(function(db) {
  var tx = db.transaction('keyval', 'readwrite');
  var keyValStore = tx.objectStore('keyval');
  keyValStore.put('bar', 'foo');
  return tx.complete;
}).then(function() {
  console.log('Added foo:bar to keyval');
}).catch(function(error) {
  console.error('Transaction failed:', error);
});
```

At this point, refreshing the browser will display two messages logged to the `Console`. And in the `Application` tab you should now see the new key/value that was set.

- - -

Next: [Quiz: Getting Started with IDB](./03-quiz-getting-started.md)