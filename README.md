# Node Explained

In this document we'll cover:

    1. The Event Loop
      a. What is single threaded?
      b. Thread Pool
      c. FAQ
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

Handling a web request

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

Example of a non-blocking task.

```javascript
    console.log('Reading file...');

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

Example of a blocking task.

```javascript
    console.log('Reading file...');

    fs.readFile('/etc/hosts', 'utf8', function (err,fileBytes) {
        console.log('The file contains ' + fileBytes.length + ' bytes');
    });

    doSomeStuffThatTakesVeryLong();

    console.log('Done.');
```

What do you think the output will be?


```
    OUTPUT:
    
    Reading file...
    Done.
    The file contains 142 bytes.
```

Lets take another look at our snippet for handling a request.

```javascript

    function handleRequest() {
        // Retrieve data from database
        sql.query('SELECT * FROM ....', function (err1, result1) {

            // Calculate averages
            calculateAverages();

            // Write data to database
            sql.query('INSERT INTO ....', function (err2, result2) {
                // Send JSON object to client
                sendJSONToClient();
            }
        }
    }
```

### FAQ

*
*
*


## Callback Hell

### The Problem

```javascript
    // Find user by username
    findUserByUsername(username, function (err, user) {
        
        // Check if user is active
        if (user.isActive) {

            // Find user permissions by user id
            findUserPermissions(user.id, function (err, permissions) {
                if (permission.contains('admin')) {
                    
                    // Insert data into database
                    sql.query('INSERT INTO ....', function (err, result) {
                        
                        // Update data in database
                        sql.query('UPDATE ....', function (err, result) {

                            // Call third party api
                            http.get(url, function (err, response) {
                                
                                // Send JSON object to client
                                sendJSONToClient();
                            });
                        });
                    });
                }else {

                    // Insert data into database
                    sql.query('INSERT INTO ....', function (err, result) {

                         // Send JSON object to client
                         sendJSONToClient();
                    });
                }
            });
        }
    });

```


    