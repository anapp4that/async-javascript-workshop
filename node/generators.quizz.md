# Question 1

Create a custom async generator that loops over the files that are passed in.

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

function* fileLoader(files) {
  for(let i = 0; i < files.length; i++){
    readFile(files[i])
    console.log(yield)
  }
},
(async () => {
  for await (let contents of fileLoader([
    "./files/demofile.txt",
    "./files/demofile.other.txt"
  ])) {
    console.log(contents);
  }
})();
```
