---
layout: post
title:  "Insertion Sort"
date:   2019-11-21 09:00:00 +0000
categories: algorithms
mathjax: true
---
A simple search algotrithem, we iterate through each element in our array starting from index=1, we store `arr[i]` in a a temp variable, we then test if the element we have on the LHS of our temp "currentValue" ie arr[0] should be on the RHS of the temp value with `arr[j] > currentValue`, if the element is already in the correct place relative to the current value we  skip over the while loop and for the sake of keeping the code simple we assign the value we took from arr[i] back to arr[i] resulting in no change. However if the currentValue is less than the previous value ie we have a bigger value at arr[i] than at arr[i+1] eg `arr[1] > arr[2]`, then we go into the while loop which shifts each value that satisfies this condition to the right by one to make a space to **insert** the value.

# Runtime: \\(O (n^2)\\) average case.
* Where n is the number of elements to sort.
* However for an already sorted array we get \\(O (n)\\), consequently this algorithm still has its place for nearly sorted data.
* [https://www.toptal.com/developers/sorting-algorithms](https://www.toptal.com/developers/sorting-algorithms)

# Quick Sort C#:
{% highlight Csharp %}
public int[] Sort(int[] arr)
{
	for (int i = 1; i < arr.Length; i++)
	{
		var currentValue = arr[i];
		var j = i - 1;

		while (j >= 0 && arr[j] > currentValue)
		{
			arr[j + 1] = arr[j];
			j--;
		}
		arr[j + 1] = currentValue;
	}

	return arr;
}
{% endhighlight %}

# Stepping through:
I reccomend doing this yourself in an IDE.

We are aiming for a sorted list:

| Index |  0  |   1  |   2  |   3  |   4  |   5  |   6  |  7   |  8   |
|:-----:|:---:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| arr   |  10 |  14  |  22  | 1000 | 1500 | 2000 | 2001 | 2002 | 2003 |

We start with:

| Index |  0  |   1  |   2  |   3  |  4   |   5  |   6  |  7  |  8  |
|:-----:|:---:|:----:|:----:|:----:|:----:|:----:|:----:|:---:|:---:|
| arr   | 22  | 1500 | 2001 | 2002 | 1000 | 2000 | 2003 | 10  | 14  |

# 



# Example tests:
{% highlight Csharp %}
[Fact]
public void InsertionSortSimpleArrayTest()
{
	int[] expected = { 10, 14, 22, 1000, 1500, 2000, 2001, 2002, 2003 };
	int[] arr = { 22, 1500, 2001, 2002, 1000, 2000, 2003, 10, 14 };

	var sort = new InsertionSort();

	var result = sort.Sort(arr);
	Assert.Equal(expected, result);
}

[Fact]
public void InsertionSortMediumSizedRandomArrayTest()
{
	int Min = int.MinValue;
	int Max = int.MaxValue;
	Random randNum = new Random();
	int[] arr = Enumerable
		.Repeat(0, 1000)
		.Select(i => randNum.Next(Min, Max))
		.ToArray();

	int[] expected = new int[arr.Length];
	arr.CopyTo(expected, 0);
	Array.Sort(expected);

	var sort = new InsertionSort();
	var result = sort.Sort(arr);
	Assert.Equal(expected, result);
}
{% endhighlight %}

# Resources:
* [SO/q/164163 Quick Sort Choosing The Pivot](https://stackoverflow.com/questions/164163/quicksort-choosing-the-pivot)
* [Cracking the coding interview 6th edition](http://www.crackingthecodinginterview.com)