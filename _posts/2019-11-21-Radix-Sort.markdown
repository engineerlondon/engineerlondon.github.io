---
layout: post
title:  "Radix Sort with Bucket Sort vs Counting sort for Sub Sorting"
date:   2020-11-21 09:00:00 +0000
categories: algorithms, sorting
---
A non-comparative sort algorithm used primarily for numbers 0-9 "buckets" or it can also be used on strings by using 26 buckets, one for each letter.
[https://www.quora.com/What-are-the-differences-between-radix-sort-and-bucket-sort](https://www.quora.com/What-are-the-differences-between-radix-sort-and-bucket-sort)
It is an-place and stablesorting algorithm.

Although this video is not the most polished I reccomend manually sorting an array using Radix sort, this short video (11m 50s) gives a great introduction to Radix sort [Youtube: Jenny's lectures CS/IT NET&JRF - Radix Sort](https://www.youtube.com/watch?v=JMlYkE8hGJM)

# Example:
[12, 22, 4, 90, 890, 1999, 4, 22]

# Runtime: *O(kn)*
* k number of digits (4) of maximum value (1999)
* n integers in the range (8)

# Binary Search C#
{% highlight Csharp %}
code
{% endhighlight %}