# promisfy

## description

this package is used for transform a node-style asynchronous function to a promise-style function. It's very handy if you are using async/await. After `v1.1.0`, you can also use it for a event callback.

a node-style asynchronous function should be like this:

```javascript
function asycFunction(arg1, arg2, callback) {
    // do something
}

// callback should be like this
function callback(err, result) {
    // do something
}
```

The first argument `err` represents whether the asynchronous call is failed(`null` while it's successful), and the second argument is the result of this call.

## install

```shell
npm install --save promisfy
```

## usage

very simple to use:

```javascript
const fs = require('fs');
const http = require('http');
const {promisfy, waitFor} = require('promisfy');

// using promisfy
const readFile = promisfy(fs.readFile);

async function main() {
    let content = await readFile('myfile.txt', {encoding:'utf8'});

    return content;
}

main().then(function(content) {
    console.log('myfile:');
    console.log(content);
})

// using waitFor
// receive post data
http.createServer(80, function(req, res) {
    async function handleRequest(req, res) {
        if (req.method === 'POST') {
            req.body = await waitFor(req.sock, 'data');
        }

        // now you can do something with req.body
    }
})
```

Be attention, `waitFor` the `data` or `stream` event will make this function add listener to `data`, `end` and `error` events. All data will be returned only if there is no `error` event triggered and the `end` event is triggered. As for other event, only the second argument will be passed to the Promise
