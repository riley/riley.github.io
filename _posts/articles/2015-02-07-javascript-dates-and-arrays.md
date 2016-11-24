---
layout: post
title: "JavaScript Date object and Arrays"
excerpt: ""
categories: articles
tags: [quick-trick, dates]
author: riley
comments: true
share: true
modified: 2015-02-07T14:18:57-04:00
---

Short post. Putting this here for myself and future googlers.

Did you know that you can easily create an array of all the days in a year? You don't have to explicitly set the month parameter in `new Date()`.


first we'll make a range function, unless you're using the underscore/loash method `_.range`
```javascript
function range(num) {
    return Array.apply(null, Array(num)).map(function (_, i) {return i;});
}
```
which will give us an array of numbers from 0-364. All we have to do to transform that into an array of `Date` objects is map them. The day parameter will automagically do a modulo into the month parameter:

```javascript
var dateArr = range(365).map(function (i) {
    // must add 1 to the date
    // January 1, 2014 would be new Date(2014, 0, 1)
    return new Date(2014, 0, i + 1);
});
```

Presto! You don't ever have to remember the number of days in each month again!

I'll leave it to you to figure out if it's a leap year or not.
