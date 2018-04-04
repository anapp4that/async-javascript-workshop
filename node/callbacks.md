# Callbacks

* Do quizz 1 first

## Handling errors, error first callbacks

* Read the below code and then do quizz 2 and 3

```js
const fs = require("fs");

fs.readFile("./demofile.txt", { encoding: "utf8" }, (err, data) => {
  if (err) {
    // next(err) <- can pass up the chain

    // console.error(err); <- can log and continue
    // return
    throw err; // <- can error and exit
  } else {
    console.log(data);
  }
});
```

## Callback Hell

```js
function doAsyncTask(cb) {
  setImmediate(() => {
    console.log("Async Task Calling Callback");
    cb();
  });
}

doAsyncTask(() => {
  doAsyncTask(() => {
    doAsyncTask(() => {
      doAsyncTask(() => {
        doAsyncTask(() => {
          doAsyncTask(() => {
            doAsyncTask(() => {
              doAsyncTask(() => {
                doAsyncTask(() => {
                  doAsyncTask(() => {});
                });
              });
            });
          });
        });
      });
    });
  });
});
```