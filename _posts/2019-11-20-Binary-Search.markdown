---
layout: post
title:  "Binary Search"
date:   2019-11-20 09:00:00 +0000
categories: algorithms
mathjax: true
---
A comparison search algorithm using a divide-and-conquer approach for use on **sorted** elements, finds the index position of a value in an array. This approach can be adapted for a range of purposes.

# Runtime: \\(O (Log n)\\)
Where n is the number of elements to search through.
[Stack Overflow: What does O(log n) mean exactly?](https://stackoverflow.com/questions/2307283)

# Binary Search C#
{% highlight Csharp %}
int BinarySearch(int[] valuesToSearch, int valueToFind){
	int high = valuesToSearch.Length -1;
	int low = 0;
	while(low <= high){
		int mid = (low + high) / 2;
		if(valuesToSearch[mid] > valueToFind)
			high = mid - 1;
		else if(valuesToSearch[mid] < valueToFind)
			low = mid + 1;
		else
			return mid;
	}
	return -1; // No matching value in the array.
}
{% endhighlight %}

* if(valuesToSearch[mid] > valueToFind) - if the value at our pivot point is greater than the value we are looking for then look in the LHS set, ie between mid -1 and low.
* if(valuesToSearch[mid] < valueToFind) - if the value at our pivot point is less than than the value we are looking for then look in the RHS set, ie between mid +1 and high.
* Continue to divide and test until we reach low > high, in which case we never found a match.
* Or valuesToSearch[mid] is neither > or < the valueToFind, in which case we must have found valueToFind, so return the index.

# Example tests:
{% highlight Csharp %}
[Fact]
public void SearchFindSecondValueArrayTest()
{
	BinarySearch search = new BinarySearch();
	int[] arr = { 10, 14, 22, 1000, 1500, 2000, 2001, 2002, 2003 };
	int result = search.Search(arr, 14);
	Assert.Equal(1, result);
}

[Fact]
public void SearchFindIntMaxValue()
{
	BinarySearch search = new BinarySearch();
	int[] arr = { 10, 14, 22, 1000, 1500, 2000, 2001, 2002, int.MaxValue };
	int result = search.Search(arr, int.MaxValue);
	Assert.Equal(8, result);
}

[Fact]
public void SearchValueDoesNotExistTest()
{
	BinarySearch search = new BinarySearch();
	int[] arr = { 10, 14, 22, 1000, 1500, 2000, 2001, 2002, 2003 };
	int result = search.Search(arr, 5);
	Assert.Equal(-1, result);
}
{% endhighlight %}

Also check out [an algorithm for finding the median element in an unsorted array in linear time, constant space](https://www.quora.com/Whats-an-algorithm-for-finding-the-median-element-in-an-unsorted-array-in-linear-time-constant-space)

[https://en.wikipedia.org/wiki/Binary_search_algorithm](https://en.wikipedia.org/wiki/Binary_search_algorithm)