---
layout: post
title:  "Overflow Hazard in Algorithms"
date:   2019-11-21 09:00:00 +0000
categories: algorithms
---

In a number of algorithms there is a risk of very lage numbers overflowing, particularly when  calculating the mid point or pivot point, (anything over 2^31 - 1) we need to handle a problem of "wrap around" / overflow in our mid point calculation we can use one of the following options, each with their pros and cons:
 * int mid = (int)((unsigned int)(low + high) / 2)
 * int mid = (low + high) \>\>\> 1 (\>\>\> in Java performs a logical-right-shift)
 * int mid = low + ((high - low) / 2)
 
[StackOverflow/q/6735259](https://stackoverflow.com/questions/6735259)
[topcoder.com community binary-search](https://www.topcoder.com/community/competitive-programming/tutorials/binary-search)