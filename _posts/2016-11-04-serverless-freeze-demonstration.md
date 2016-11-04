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

<script src="https://gist.github.com/jtslear/4347099f7c48550b43fb3efdef2271eb.js"></script>

We initialize 2 empty arrays, A and B, outside of the handler.  When the function
is run, these two arrays will remain in memory when the function is frozen.
Inside of the hander we've got 2 more empty arrays, C and D.  During the
execution of the function we simply push a string item to it and for testing, we
clear our arrays A and C.  Let's look at what this does when AWS invokes this
function three times:

<script src="https://gist.github.com/jtslear/284aaeecd3cfd9545683fc45affe6599.js"></script>

You'll notice in the first run, we have one pushed object to all four arrays.
In the second run, arrayB has a second object in the array, and in the third
run, arrayB continues to add the array.  Array A doesn't exhibit this behavior
because we clean him out prior to stopping the function.  Arrays C and D
technically get reinitialized at every run.

With this small demo, we can see some value and some potential headache when
created incorrectly.  This could be a great strategy if one needs to do
something expensive, such as setup a database connection.  Inside of the
handler, we can call upon that object to reconnect to the database without
needing to go through the heavy lifting every single time.

To avoid leaking memory, one would want to ensure as much data as possible is
located within the handler to minimize problems.

### Recommended Reading:
* [How Does AWS Lambda Run My Code? The Container
  Model](http://docs.aws.amazon.com/lambda/latest/dg/lambda-introduction-function.html#topic3)
