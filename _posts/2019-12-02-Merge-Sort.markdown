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

# Merge Sort C#:
{% highlight Csharp %}
public class MergeSort
{
	public void Sort(int[] arr)
	{
		int[] helper = new int[arr.Length];
		DoMergeSort(arr, helper, 0, arr.Length - 1);
	}

	private void DoMergeSort(int[] arr, int[] helper, int left, int right)
	{
		if (left < right)
		{
			int mid = (right + left) / 2;
			DoMergeSort(arr, helper, left, mid);
			DoMergeSort(arr, helper, mid + 1, right);
			Merge(arr, helper, left, mid, right);
		}
	}

	private void Merge(int[] arr, int[] helper, int left, int mid, int right)
	{
		// Copy both halves into the helper array.
		for (int i = left; i <= right; i++)
		{
			helper[i] = arr[i];
		}

		int helperLeft = left;
		int helperRight = mid + 1;
		int current = left;

		while (helperLeft <= mid && helperRight <= right)
		{
			if (helper[helperLeft] <= helper[helperRight])
			{
				arr[current] = helper[helperLeft];
				helperLeft++;
			}
			else
			{
				arr[current] = helper[helperRight];
				helperRight++;
			}
			current++;
		}

		int remaining = mid - helperLeft;
		for (int i = 0; i <= remaining; i++)
		{
			arr[current + i] = helper[helperLeft + i];
		}
	}
}
{% endhighlight %}

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

Resources:
* [Cracking the coding interview 6th edition](http://www.crackingthecodinginterview.com)