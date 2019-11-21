---
layout: post
title:  "Binary Search"
date:   2019-11-21 09:00:00 +0000
categories: algorithms
---
A comparison search algorithm using a divide-and-conquer approach for use on **sorted** elements, finds the index position of a value in an array. This approach can be adapted for a range of purposes.

# Runtime: *O (Log N)*
Where n is the number of elements to search through.

# Quick Sort C#
{% highlight Csharp %}
int QuickSort(int[] valuesToSearch, int valueToFind){
	int high = valuesToSearch.Length -1;
	int low = 0;
	while(low <= high){
		int mid = (low + high) / 2;
		if(valuesToSearch[mid] > valueToFind) // if the value at our pivot point is greater than the value we are looking for then look in the LHS set.
		{
			high = mid - 1;
		}
		if(valuesToSearch[mid] < valueToFind) // if the value at our pivot point is less than than the value we are looking for then look in the RHS set.
		{
			low = mid + 1;
		}
		else{
			return mid;
		}
	}
	return -1; // No matching value in the array.
}
{% endhighlight %}

* In Java and account for possible overflow see [StackOverflow/q/6735259](https://stackoverflow.com/questions/6735259):
	* mid = (low + high) >>> 1 or 
	* mid = low + ((high - low) / 2)


[https://en.wikipedia.org/wiki/Binary_search_algorithm](https://en.wikipedia.org/wiki/Binary_search_algorithm)