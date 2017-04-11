# Node Explained

In this document we'll cover:

    1. The Event Loop
      a. What is single threaded?
      b. Thread Pool
      c. Example and FAQ
    2. Callback Hell
      a. The Problem
      b. The Solution
      c. Frameworks

## The Event Loop

### What is single threaded?

A single threaded application can be thought of as a long line at a cash register. Everything executes sequentially in order. The important take away is **YOUR** application does one thing at a time.

```javascript
    console.log('Reading file...');
    var fileBytes = fs.readFileSync(filename, "utf8");
    console.log('The file contains ' + fileBytes.length + ' bytes.');
    console.log('Done.');
```

```
    OUTPUT:

    Reading file...
    The file contains 142 bytes.
    Done.
```

```javascript
    function handleRequest() {
        // Retrieve data from database

        // Calculate averages

        // Write data to database

        // Send JSON object to client

    }
```

In the example above, no requests will be processed while data is being retrieve or written to the database. Node is single threaded and can only perform **ONE** action at a time. 

This will reduce the number of request that your apllication can handle per second.

### Thread Pool

Apart from your application, Node runs an IO and Network thread pool which consists of 4 (by default) threads.

These threads can be used to offload IO and Network tasks.

```javascript
    fs.readFile('/etc/hosts', 'utf8', function (err,fileBytes) {
        console.log('The file contains ' + fileBytes.length + ' bytes');
    });

    console.log('Done.');
```

```
    OUTPUT:
    
    Reading file...
    Done.
    The file contains 142 bytes.
    
```




    