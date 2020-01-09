---
layout: post
title:  "Merge Sort"
date:   2019-12-02 09:00:00 +0000
categories: algorithms
mathjax: true
---
Merge Sort avoids the risk of Quick Sort's worst case \\(O(n^2)\\) runtime, however Merge Sort makes use of an additional Array to perform the sorting, resulting in at least \\(O(n)\\), memory usage.

# Runtime: \\(O (n Log n)\\) average and worst case.
* Where n is the number of elements to sort.

# Quick Sort C#:
{% highlight Csharp %}

{% endhighlight %}

# Stepping through:
I reccomend doing this yourself in an IDE.


# Example tests:
{% highlight Csharp %}
[Test]
public void MergeSortSimpleArrayTest()
{
	int[] expected = { 10, 14, 22, 1000, 1500, 2000, 2001, 2002, 2003 };
	int[] arr = { 22, 1500, 2001, 2002, 1000, 2000, 2003, 10, 14 };

	var mergeSort = new MergeSort();

	mergeSort.Sort(arr);
	Assert.AreEqual(expected, arr);
}
{% endhighlight %}

# Resources
-
