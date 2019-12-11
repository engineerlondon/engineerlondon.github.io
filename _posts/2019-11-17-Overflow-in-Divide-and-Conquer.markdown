---
layout: post
title:  "Overflow in Divide-and-Conquer"
date:   2019-11-17 09:00:00 +0000
categories: algorithms
---

### [Google's Joshua Bloch posted on this subject in 2006](https://ai.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html)
A divide-and-conquer algorithms usually rely on calculating a mid point, historically in most implementations even in major SDKs there has been a risk of a runtime error occurring due to the addition of two large integers, overflowing the upper bound of Int32 when calculating the mid point or pivot point.

We need to handle the problem of "wrap around" / overflow resulting in a negative mid point value, we can use one of the following options:
 * `int mid = (int)((unsigned int)(low + high) / 2);`
 * or the same in C# `int mid = (int)((uint)(low + high) / 2);`
 * `int mid = (low + high) \>\>\> 1;` (in Java \>\>\> performs a logical-right-shift)
 * `int mid = low + ((high - low) / 2)`

With one of the above we guarantee that when the sum of low and high is greater than the maximum positive int value \\((2^31\) - 1\\) the pivot point / mid point calculated is still positive.

* [StackOverflow/q/6735259](https://stackoverflow.com/questions/6735259)
* [topcoder.com community binary-search](https://www.topcoder.com/community/competitive-programming/tutorials/binary-search)
