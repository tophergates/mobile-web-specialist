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
  dbPromise.then(function(db) {
    var tx = db.transaction('keyval', 'readwrite');
    var keyValStore = tx.objectStore('keyval');
    keyValStore.put('X', 'favoriteAnimal');
    return tx.complete;
  }).then(function(){
    console.log('Added favoriteAnimal:X to keyval');
  });
  ```

  </p>
</details>

---

Next: [Quiz: More IDB](./04-quiz-more-idb.md)
