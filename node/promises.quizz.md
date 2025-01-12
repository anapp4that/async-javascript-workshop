# Question 1 - (10min)

Create a promise version of the async readFile function

```js
const fs = require("fs");

function readFile(filename, encoding) {
    return new Promise((resolve, reject) => {
        fs.readFile(filename, encoding, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve(data);
            }
        });
    });
}
readFile("./files/demofile.txt", "utf-8").then(
    data => {
        console.log(data);
    },
    err => {
        console.error("Failed to read file", err);
    }
);

//or use promisify to make apromise out of a error first call back functions
const fs = require("fs");
const util = require("util");
const readFile = util.promisify(fs.readFile);

readFile("./files/demofile.txt", "utf-8").then(
    data => {
        console.log(data);
    },
    err => {
        console.error(err);
    }
);
// });

```

# Question 2

Load a file from disk using readFile and then compress it using the async zlib node library, use a promise chain to process this work.

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
    return new Promise((resolve, reject) => {
        zlib.gzip(data, (err, result) => {
            if (err) {
                reject(err);
            } else {
                resolve(result);
            }
        });
    });
}

function readFile(filename, encoding) {
    return new Promise((resolve, reject) => {
        fs.readFile(filename, encoding, (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    });
}

readFile("./files/demofile.txt", "utf-8").then(
    data => {
        zlibPromise(data).then(
            res => console.log(res),
            err => console.error(err)
        );
    },
    err => console.error(err)
); // --> Load it then zip it and then print it to screen
```

# Question 3

Convert the previous code so that it now chains the promise as well.
```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
    return new Promise((resolve, reject) => {
        zlib.gzip(data, (err, result) => {
            if (err) {
                reject(err);
            } else {
                resolve(result);
            }
        });
    });
}

function readFile(filename, encoding) {
    return new Promise((resolve, reject) => {
        fs.readFile(filename, encoding, (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    });
}

readFile("./files/demofile.txt", "utf-8")
    .then(
        data => {
            return zlibPromise(data);
        },
        err => {
            console.error(err);
        }
    )
    .then(
        data => {
            console.log(data);
        },
        err => {
            console.error(err);
        }
    );
//Or you can promisify the zlib.gzip function and just return that call
```

# Question 4

Convert the previous code so that it now handles errors using the catch handler
```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
    return new Promise((resolve, reject) => {
        zlib.gzip(data, (err, result) => {
            if (err) {
                reject(err);
            } else {
                resolve(result);
            }
        });
    });
}

function readFile(filename, encoding) {
    return new Promise((resolve, reject) => {
        fs.readFile(filename, encoding, (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    });
}

readFile("./files/demofile.txt", "utf-8")
    .then(data => {
        return zlibPromise(data);
    })
    .then(data => {
        console.log(data);
    })
    .catch(err => {
        console.error(err);
    });
```


# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function readFileFake(sleep) {
  return new Promise(resolve => setTimeout(resolve, sleep));
}

function killFileRead(sleep){
  return new Promise((_, reject) => setTimeout(reject, sleep, "timeout"));
}

Promise.race([readFileFake(5000), killFileRead(1000)])
    .then((data) => console.log(data))
    .catch((err) => console.log(err)); // This resolves a promise after 5 seconds, pretend it's a large file being read from disk
```

# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 1 seconds. Use `catch` to handle errors and timeouts.

```js
function authenticate() {
  console.log("Authenticating");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log("Publishing");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 403 }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, "timeout"));
}

Promise.race( [publish(), timeout(1000)])
  .then( res => {
      if (res.status === 403) {
        return authenticate();
      }
    })
  .then( _ => console.log("Published"))
  .catch( err =>{
    if(err == "timeout"){
      console.log("Request Time out")
    }
    else{
      console.error(err)
    }
  });
```
