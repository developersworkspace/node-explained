# Node Explained

In this document we'll cover:

1. The Event Loop
    1. What is single threaded?
    2. Thread Pool
    3. FAQ
2. Callback Hell
    1. The Problem
    2. The Solution
    3. Frameworks
    4. CO Example

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

                // Check for error
                if (err) {
                    console.error(err);
                    return;
                }

                if (permission.contains('admin')) {
                    
                    // Insert data into database
                    sql.query('INSERT INTO ....', function (err, result) {

                        // Check for error
                        if (err) {
                            console.error(err);
                            return;
                        }
                        
                        // Update data in database
                        sql.query('UPDATE ....', function (err, result) {

                            // Check for error
                            if (err) {
                                console.error(err);
                                return;
                            }

                            // Call third party api
                            http.get(url, function (err, response) {

                                // Check for error
                                if (err) {
                                    console.error(err);
                                    return;
                                }
                                
                                // Send JSON object to client
                                sendJSONToClient();
                            });
                        });
                    });
                }else {

                    // Insert data into database
                    sql.query('INSERT INTO ....', function (err, result) {

                        // Check for error
                        if (err) {
                            console.error(err);
                            return;
                        }

                         // Send JSON object to client
                         sendJSONToClient();
                    });
                }
            });
        }
    });
```


### The Solution

```javascript
    // Find user by username
    findUserByUsername(username).then(function(user) {

        // Check if user is active
        if (user.isActive) {

            // Find user permissions by user id
            return findUserPermissions(user.id);
        }

    }).then(function (permission) {
         if (permission.contains('admin')) {

             // Insert data into database
             return sql.query('INSERT INTO ....');

         } else {

             // Insert data into database
             return sql.query('INSERT INTO ....');
        
         }
    }).then(function (result) {

        // Call third party api
        return http.get(url);

    }).then(function (response) {

        // Send JSON object to client
        sendJSONToClient();

    }).catch(function (err) {
        console.error(err);
    });
```

### Frameworks

Frameworks to make async calls read better:

* CO
* BlueBird
* Async.js


### CO Example

```javascript
co(function* () {
    // Find user by username
    var user = yield findUserByUsername(username);

    // Check if user is active
    if (user.isActive) {

        // Find user permissions by user id
        var permissions = yield findUserPermissions(user.id);

        if (permission.contains('admin')) {

            // Insert data into database
            var result = yield sql.query('INSERT INTO ....');

            var response = yield http.get(url);

            // Send JSON object to client
            sendJSONToClient();

         } else {

            // Insert data into database
            var result = yield sql.query('INSERT INTO ....');
        
         }
    }
});
```


The MIT License (MIT)
=====================

Copyright © `2017` `Barend Erasmus`

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the “Software”), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

    