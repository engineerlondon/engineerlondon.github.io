---
layout: post
title:  "Insertion Sort"
date:   2019-11-17 09:00:00 +0000
categories: algorithms
mathjax: true
---
A simple search algorithm, we iterate through each element in our array starting from index=1, we store `arr[i]` in a a temp variable, we then test if the element we have on the LHS of our temp "currentValue" ie arr[0] should be on the RHS of the temp value with `arr[j] > currentValue`, if the element is already in the correct place relative to the current value we  skip over the while loop and for the sake of keeping the code simple we assign the value we took from arr[i] back to arr[i] resulting in no change. However if the currentValue is less than the previous value ie we have a bigger value at arr[i] than at arr[i+1] eg `arr[1] > arr[2]`, then we go into the while loop which shifts each value that satisfies this condition to the right by one to make a space to **insert** the value.

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
I recommend doing this yourself in an IDE.

We are aiming for a sorted list:

| Index |  0  |   1  |   2  |   3  |   4  |   5  |   6  |  7   |  8   |
|:-----:|:---:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| arr   |  10 |  14  |  22  | 1000 | 1500 | 2000 | 2001 | 2002 | 2003 |

We start with:

| Index |  0  |   1  |   2  |   3  |  4   |   5  |   6  |  7  |  8  |
|:-----:|:---:|:----:|:----:|:----:|:----:|:----:|:----:|:---:|:---:|
| arr   | 22  | 1500 | 2001 | 2002 | 1000 | 2000 | 2003 | 10  | 14  |

* First time through the loop currentValue is 1500. j = 0, `22 > 1500` == false so we skip the loop and arr[j + 1] = currentValue, ie no change is made.
* Second time currentValue = 2001, j = 1 `1500 > 2001` == false so no change.
* Third 2001 > 2002 == false so no change.
* Fourth currentValue = 1000, j = 3, `2002 > 1000` == true so this is our first element to move, we go into the loop, and shift arr[j] right 1 onto  arr[j + 1] ie replacing 1000 with 2002, then `j--`.
* Go through the loop again arr[2] `2001 > 1000` so 2001 is shifted to the right one place, over the top of where 2002 was, then `j--`.
* arr[1] `1500 > 1000` so 1500 is shifted right one place and `j--`.
* j = 0, we test `arr[0] > currentValue` ie `22 > 1000` == false so we exit the while loop and **insert** our temp/currentValue 1000 into arr[j + 1]. 

| Index  |  0  |     1    |    2   |    3   |     4    |   5  |   6  |  7  |  8  |
|:------:|:---:|:--------:|:------:|:------:|:--------:|:----:|:----:|:---:|:---:|
| Before | 22  |   1500   |  2001  |  2002  | **1000** | 2000 | 2003 | 10  | 14  |
| j = 3  | 22  |   1500   |  2001  | `2002` |  `2002`  | 2000 | 2003 | 10  | 14  |
| j = 2  | 22  |   1500   | `2001` | `2001` |   2002   | 2000 | 2003 | 10  | 14  |
| j = 1  | 22  |   `1500` | `1500` |  2001  |   2002   | 2000 | 2003 | 10  | 14  |
| Insert | 22  | **1000** |  1500  |  2001  |   2002   | 2000 | 2003 | 10  | 14  |

* i = 5, j = i - 1, so j = 4, `2002 > 2000` so we shift 2002 right 1, `j--`
* j = 3 `2001 > 2000` so we shift right again and `j--`

| Index  |  0  |  1   |    2  |     3    |     4   |     5    |   6  |  7  |  8  |
|:------:|:---:|:----:|:-----:|:--------:|:-------:|:--------:|:----:|:---:|:---:|
| Before | 22  | 1000 |  1500 |   2001   |   2002  | **2000** | 2003 | 10  | 14  |
| j = 4  | 22  | 1000 |  1500 |   2001   |  `2002` |  `2002`  | 2003 | 10  | 14  |
| j = 3  | 22  | 1000 |  1500 |  `2001`  |  `2001` |   2002   | 2003 | 10  | 14  |
| Insert | 22  | 1000 |  1500 | **2000** |   2001  |   2002   | 2003 | 10  | 14  |

* 2003 is in the correct place so we do not enter the while loop.
* i = 7, j = 6, currentValue = 10, we go through the loop decrementing j until j = -1, at which point `j >= 0` == false, we stop and set `arr[ j + 1] = currentValue`.

| Index  |  0      |    1   |    2   |   3    |    4   |   5    |    6   |   7    |  8  |
|:------:|:-------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:---:|
| Before |   22    |  1000  |  1500  |  2000  | 2001   |  2002  |  2003  | **10** | 14  |
| j = 6  |   22    |  1000  |  1500  |  2000  | 2001   |  2002  | `2003` | `2003` | 14  |
| j = 5  |   22    |  1000  |  1500  |  2000  | 2001   | `2002` | `2002` |  2003  | 14  |
| j = 4  |   22    |  1000  |  1500  |  2000  | `2001` | `2001` |  2002  |  2003  | 14  |
| j = 3  |   22    |  1000  |  1500  | `2000` | `2000` |  2001  |  2002  |  2003  | 14  |
| j = 2  |   22    |  1000  | `1500` | `1500` | 2000   |  2001  |  2002  |  2003  | 14  |
| j = 1  |   22    | `1000` | `1000` |  1500  | 2000   |  2001  |  2002  |  2003  | 14  |
| j = 0  |  `22`   |  `22`  |  1000  |  1500  | 2000   |  2001  |  2002  |  2003  | 14  |
| Insert | **10**  |   22   |  1000  |  1500  | 2000   |  2001  |  2002  |  2003  | 14  |

* Last but not least we have i = 8, j = 7, currentValue = 14
* We enter the while loop and shift elements to the right one place until j = 0 `arr[j] > 14` == false so we move on.

| Index  |  0  |    1   |    2   |   3    |    4   |   5    |    6   |   7    |    8    |
|:------:|:---:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:-------:|
| Before | 10  |   22   |  1000  |  1500  | 2000   |  2001  |  2002  |  2003  | **14**  |
| j = 7  | 10  |   22   |  1000  |  1500  | 2000   |  2001  |  2002  | `2003` |  `2003` |
| j = 6  | 10  |   22   |  1000  |  1500  | 2000   |  2001  | `2002` | `2002` |   2003  |
| j = 5  | 10  |   22   |  1000  |  1500  | 2000   | `2001` | `2001` |  2002  |   2003  |
| j = 4  | 10  |   22   |  1000  |  1500  | `2000` | `2000` |  2001  |  2002  |   2003  |
| j = 3  | 10  |   22   |  1000  | `1500` | `1500` |  2000  |  2001  |  2002  |   2003  |
| j = 2  | 10  |   22   | `1000` | `1000` |  1500  |  2000  |  2001  |  2002  |   2003  |
| j = 1  | 10  |  `22`  |  `22`  |  1000  |  1500  |  2000  |  2001  |  2002  |   2003  |
| Insert | 10  | **14** |   22   |  1000  |  1500  |  2000  |  2001  |  2002  |   2003  |

i = 9, array Length = 9 so our outer for loop `for (int i = 1; i < arr.Length; i++)` is finished and we return the sorted array.

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
* [Hacker Rank - insertionsort ](https://www.hackerrank.com/challenges/insertionsort2/problem)