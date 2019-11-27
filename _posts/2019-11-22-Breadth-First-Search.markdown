---
layout: post
title:  "Breadth First Search"
date:   2019-11-21 09:00:00 +0000
categories: algorithms, searching
---
A non-comparative sort algorithm used primarily for numbers 0-9 "buckets" or it can also be used on strings by using 26 buckets, one for each letter.
[https://www.quora.com/What-are-the-differences-between-radix-sort-and-bucket-sort](https://www.quora.com/What-are-the-differences-between-radix-sort-and-bucket-sort)
It is an-place and stablesorting algorithm.

Although this video is not the most polished I reccomend manually sorting an array, this short video (11m 50s) gives a great introduction to Radix sort [Youtube: Jenny's lectures CS/IT NET&JRF - Radix Sort](https://www.youtube.com/watch?v=JMlYkE8hGJM)

# Example:
[12, 22, 4, 90, 890, 1999, 4, 22]

# Runtime: *O(kn)*
* k number of digits (4) of maximum value (1999)
* n integers in the range (8)

# C#
{% highlight Csharp %}
public int[] Sort(int[] arr)
{
	int[] tmp = new int[arr.Length];
	for (int shift = 31; shift > -1; --shift)
	{
		var j = 0;
		int i;
		for (i = 0; i < arr.Length; ++i)
		{
			bool move = arr[i] << shift >= 0;
			if (shift == 0 ? !move : move)
				arr[i - j] = arr[i];
			else
				tmp[j++] = arr[i];
		}
		Array.Copy(tmp, 0, arr, arr.Length - j, j);
	}

	return arr;
}
{% endhighlight %}
[Source: w3 Resource](https://www.w3resource.com/csharp-exercises/searching-and-sorting-algorithm/searching-and-sorting-algorithm-exercise-10.php)
[Shared under create commons licence](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US)


# Example tests:
{% highlight Csharp %}
[Fact]
public void RadixSortSimpleArrayTest()
{
	int[] expected = { 10, 14, 22, 1000, 1500, 2000, 2001, 2002, 2003 };
	int[] arr = { 22, 1500, 2001, 2002, 1000, 2000, 2003, 10, 14 };

	var sort = new RadixSort();

	var result = sort.Sort(arr);
	Assert.Equal(expected, result);
}

[Fact]
public void RadixSortLargeRandomArrayTest()
{
	int Min = int.MinValue;
	int Max = int.MaxValue;
	Random randNum = new Random();
	int[] arr = Enumerable
		.Repeat(0, 100000)
		.Select(i => randNum.Next(Min, Max))
		.ToArray();

	int[] expected = new int[arr.Length];
	arr.CopyTo(expected, 0);
	Array.Sort(expected);

	var sort = new RadixSort();
	var result = sort.Sort(arr);
	Assert.Equal(expected, result);
}
{% endhighlight %}

# Resources
* [Hacker Rank counting sort](https://www.hackerrank.com/challenges/countingsort1/problem)