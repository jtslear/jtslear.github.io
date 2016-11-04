---
layout: dark-post
title: Demonstrating Memory Behavior for AWS Lambda Function Freezing
type: post
published: true
status: published
categories: []
tags:
  - serverless
  - aws
  - lambda
  - functions as a service
  - faas
---

AWS indicates that when a function is executed, there are some things that get
leftover between runs.  The execution environment, which AWS indicates is a
container model, contains everything necessary to run the function.  After a
function is invoked for the first time, it will "freeze" this environment such
that it can quickly be called a second time, which will help improve the
performance of subsequent runs by avoiding the initialization step of getting
your code out into this execution environment.  This container reuse, however,
can be a benefit or a downside depending on how you write your function.  One of
these is related to the memory usage of an execution environment.

Say you have the following code:

```js
'use strict';

var arrayA = [];
var arrayB = [];

module.exports.test = (event, context, callback) => {
  var arrayC = [];
  var arrayD = [];

  arrayA.push("A");
  arrayB.push("B");
  arrayC.push("C");
  arrayD.push("D");

  console.log("Array A:", arrayA);
  console.log("Array B:", arrayB);
  console.log("Array C:", arrayC);
  console.log("Array D:", arrayD);

  arrayA = [];
  arrayC = [];

  callback(null);
};
```

We initialize 2 empty arrays, arrayA and arrayB, outside of the handler.  When the function
is run, these two arrays will remain in memory when the function is frozen.
Inside of the hander we've got 2 more empty arrays, arrayC and arrayD.  During the
execution of the function we simply push a string item to it and for testing, we
clear our arrays arrayA and arrayC.  Let's look at what this does when AWS invokes this
function three times:

```bash
serverless-freezing-demonstration % serverless invoke -f test -s dev -l
null
--------------------------------------------------------------------
START RequestId: 778bcf95-a285-11e6-aa5a Version: $LATEST
2016-11-04 07:54:40.248	778bcf95-a285-11e6-aa5a	Array A: [ 'A' ]
2016-11-04 07:54:40.284	778bcf95-a285-11e6-aa5a	Array B: [ 'B' ]
2016-11-04 07:54:40.284	778bcf95-a285-11e6-aa5a	Array C: [ 'C' ]
2016-11-04 07:54:40.284	778bcf95-a285-11e6-aa5a	Array D: [ 'D' ]
END RequestId: 778bcf95-a285-11e6-aa5a
REPORT RequestId: 778bcf95-a285-11e6-aa5a	Duration: 37.54 ms	Billed Duration: 100 ms 	Memory Size: 128 MB	Max Memory Used: 14 MB

serverless-freezing-demonstration % serverless invoke -f test -s dev -l
null
--------------------------------------------------------------------
START RequestId: 7b760204-a285-11e6-bf4a Version: $LATEST
2016-11-04 07:54:46.714 7b760204-a285-11e6-bf4a	Array A: [ 'A' ]
2016-11-04 07:54:46.714 7b760204-a285-11e6-bf4a	Array B: [ 'B', 'B' ]
2016-11-04 07:54:46.714 7b760204-a285-11e6-bf4a	Array C: [ 'C' ]
2016-11-04 07:54:46.714 7b760204-a285-11e6-bf4a	Array D: [ 'D' ]
END RequestId: 7b760204-a285-11e6-bf4a
REPORT RequestId: 7b760204-a285-11e6-bf4a	Duration: 0.68 ms	Billed Duration: 100 ms 	Memory Size: 128 MB	Max Memory Used: 14 MB

serverless-freezing-demonstration % serverless invoke -f test -s dev -l
null
--------------------------------------------------------------------
START RequestId: 7ecbbbed-a285-11e6-99ff Version: $LATEST
2016-11-04 07:54:52.288 7ecbbbed-a285-11e6-99ff	Array A: [ 'A' ]
2016-11-04 07:54:52.289 7ecbbbed-a285-11e6-99ff	Array B: [ 'B', 'B', 'B' ]
2016-11-04 07:54:52.289 7ecbbbed-a285-11e6-99ff	Array C: [ 'C' ]
2016-11-04 07:54:52.289 7ecbbbed-a285-11e6-99ff	Array D: [ 'D' ]
END RequestId: 7ecbbbed-a285-11e6-99ff
REPORT RequestId: 7ecbbbed-a285-11e6-99ff	Duration: 0.52 ms	Billed Duration: 100 ms 	Memory Size: 128 MB	Max Memory Used: 14 MB
```
You'll notice in the first run, we have one pushed object to all four arrays.
In the second run, arrayB has a second object in the array, and in the third
run, arrayB continues to add the array.  arrayA doesn't exhibit this behavior
because we clean him out prior to stopping the function.  arrayC and arrayD
technically get reinitialized at every run.

With this small demo, we can see some value and some potential headache when
created incorrectly.  This could be a great strategy if we need to do
something expensive, such as setup a database connection.  Inside of the
handler, we can call upon that object to reconnect to the database without
needing to go through the heavy lifting every single time.

To avoid leaking memory, we would want to ensure as much data as possible is
located within the handler to minimize problems.

### Recommended Reading:
* [How Does AWS Lambda Run My Code? The Container
  Model](http://docs.aws.amazon.com/lambda/latest/dg/lambda-introduction-function.html#topic3)
