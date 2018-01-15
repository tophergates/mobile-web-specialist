# 04.01: INTRODUCING THE IDB PROMISED LIBRARY
When the user opens Wittr, we want to start by showing the latest post the device received - before going to the network. Then, we make the `Web Socket` connection and start receiving new posts one by one. 

When we receive these posts, we want to display them, of course. But also we want to add them to the set of posts we already have stored. We also want to remove entries that are too old to be worth keeping.

A database is the best model for this. It means we can add and remove individual posts as needed, iterate over them, and query the data.

The web platform has a database called `IndexedDB` (IDB). It's fair to say that it has a bit of a bad reputation, but hopefully we can break through that in this chapter.

We aren't going to touch our `Service Worker` script for a bit, instead we will concentrate on learning `IDB`'s shape and how it ticks. Then, we'll integrate it into a whole `offline first` experience.

## NOSQL
If you've used `NoSQL Databases` before, `IDB` will seem familiar. But if you've only used `Relational Databases` before, the concepts might seem a little weird. And if you've never used databases before, well, that might even help! As you can come to `IDB` fresh, without without trying to translate it to similar but, ultimately, different things you've encountered.

## INDEXEDDB DATABASE SHAPE
With `IDB`, you can have multiple databases with whatever name you give them. But we're only going to be creating one. Generally, you'll only have one database per application.

That database contains multiple object stores; generally one for each kind of thing you want to store. An object store can contain multiple values. These can be JavaScript `objects`, `strings`, `numbers`, `dates`, or `arrays`.

Items in the object's store can have a separate primary key or you can assign a property of the values to be the key. The key must be unique within an object store. It becomes the way you identify a particular object.

**Out-of-line Keys**

| Key | Value |
| --- | ----- |
| **Jane** | 41 |
| **Natalie** | 24 |
| **Pete** | 32 |

**In-line Keys**

| Entries |
| ------- |
| { **name: 'Jane'**, age: 41 } |
| { **name: 'Natalie'**, age: 24 } |
| { **name: 'Pete'**, age: 32 } |

Later, you can `get`, `set`, `add`, `remove`, or `iterate` over items in object stores as part of a `transaction`. All read or write operations in `IndexedDB` must be part of a `transaction`.

**Examples of Transactions:**
  - [ ] Add "hello:world" to "keyval" store
  - [ ] Get the first value from the "people" store
  - [ ] Change the person's name to "James"
  - [ ] Write it back to the "people" store

This means that if you create a `transaction` for a series of steps and one of the actions fail, none of them are applied. The state of the database would be as if none of the steps happened.

You can also create indexes within an object store, which provides a different view of the same data ordered by particular properties:

**People Store:**

| Entries |
| ------- |
| { **name: 'Jane'**, age: 41 } |
| { **name: 'Natalie'**, age: 24 } |
| { **name: 'Pete'**, age: 32 } |

*...indexed by age:*

| Entries |
| ------- |
| { **age: 24**, name: 'Natalie' } |
| { **age: 32**, name 'Pete' } |
| { **age: 32**, name: 'Jane' } |

The model here is similar to a lot of databases, which makes a lot of sense. The browser support is good as well, with every major browser supporting it. So, why does `IndexedDB` have such a bad reputation??

Unfortunately, the API is a little... *horrid* and often creates spaghetti code. It's all asynchronous, which is fine, but it pre-dates `Promise`s. So it kind of invented its own event-based Promise system which creates really confusing code. To avoid this clumsy API, we're going to use a really small library that mirrors the `IndexedDB API`, but uses `Promise`s rather than events. The library is called `IndexedDB Promised`. Anything you use in this library, you can apply to using `IndexedDB` directly.

Take a look at this libraries repository and examples here: https://github.com/jakearchibald/idb

Next, we'll take a look at how you create a real-life database in the browser.

- - -

Next: [Getting Started with IDB](./02-getting-started.md)