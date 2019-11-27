---
layout: post
title:  "Quick Sort"
date:   2019-11-18 09:00:00 +0000
categories: algorithms
mathjax: true
---
A very common and efficient in-place, comparison sorting algorithm, usually implemented with recursion, swaps elements around a pivot. For best performance the choice of pivot is important.

# Runtime: \\(O (n Log n)\\) average case, worst case \\(O(n^2)\\).
* Where n is the number of elements to sort.
* If the pivot selected in Partition is at the start or end of the array we will get worst case performance on an already sort a sorted array, something that happens more than might be expected.
* We also get poor performance when trying to sort an array with a large number of repeated elements.
* In the below I have tried to highlight the depth of call stack to give an appreciation of the fact that despite the multiple while loops this implementation heavily relies on recursion.
* Benchmarking of a recursive Quick Sort implementation out performs an iterative approach. [SO/q/12553238 Quick Sort iterative or recursive](https://stackoverflow.com/questions/12553238/quicksort-iterative-or-recursive)

# Ways of learning
Individual's learning styles are different, there are a huge verity of choices out there, this is what worked for me.

I have re-created one of a number of solutions to Quick Sort below and stepped through the code using a unit test, also provided, this detailed step through example is how I originally got to grips with Quick Sort, I recommend doing it yourself, reading the below might be useful as a refresher, but to really embed the understanding take the time to write it yourself, write a test and give it your best shot, once you have struggled for a good while then check back against an example, then walk through it like I have below, and to really cement the understanding write out what is happening as you step through.

# Quick Sort C#:
{% highlight Csharp %}
public class QuickSort
    {
        private int[] _arr;

        public QuickSort(int[] arr)
        {
            _arr = arr;
        }

        public int[] Sort()
        {
            return Sort(0, _arr.Length - 1);
        }

        private int[] Sort(int left, int right)
        {
            int index = Partition(left, right);
            if (left < index - 1)
                Sort(left, index - 1); // Sort LHS
            if (index < right)
                Sort(index, right); // Sort RHS
            return _arr;
        }

        private int Partition(int left, int right)
        {
            int pivot = _arr[(left + right) / 2];

            while (left <= right)
            {
                while (_arr[left] < pivot)
                    left++;
                while (_arr[right] > pivot)
                    right--;

                if (left <= right)
                {
                    Swap(left, right);
                    left++;
                    right--;
                }
            }

            return left;
        }

        private void Swap(int left, int right)
        {
            var temp = _arr[left];
            _arr[left] = _arr[right];
            _arr[right] = temp;
        }
    }
{% endhighlight %}

# Stepping through:
I reccomend doing this yourself in an IDE.

We are aiming for a sorted list:

| Index |  0  |   1  |   2  |   3  |   4  |   5  |   6  |  7   |  8   |
|:-----:|:---:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| arr   |  10 |  14  |  22  | 1000 | 1500 | 2000 | 2001 | 2002 | 2003 |

We start with:

| Index |  0  |   1  |   2  |   3  |    4   |   5  |   6  |  7  |  8  |
|:-----:|:---:|:----:|:----:|:----:|:------:|:----:|:----:|:---:|:---:|
| arr   | 22  | 1500 | 2001 | 2002 | `1000` | 2000 | 2003 | 10  | 14  |

# Sort calls Partition:
* We pick the middle of the array for our partition as this reduces the risk of the worst case performance O(n^2) ie: `int left = 0, right = arr.Length -1`.
* `int pivot = _arr[(left + right) / 2]` Eg: *pivot = 1000*.
* `while (left <= right)` Check that the pointers that we will use to choose elements to swap are still valid. Quick Sort will work with values around the current pivot until this is not longer satisfied, then partition again, selecting a new pivot in the proccess.
* `while (_arr[left] < pivot) left++` Find a value on the LHS of the pivot that should be on the RHS. ie greater than 1000 for our first pass, the first is left = 1, **arr[1] = 1500**.
* `while (_arr[right] > pivot) right--` Does the same for the value on the RHS that should be on the LHS. ie take the first value that is less than 1000, in this case **arr[8] = 14**.
* `if (left <= right)` We only swap the values chosen if the index in left, is equal to or lower than the index in right. This is the same test as the earlier `while (left <= right)`   
* `Swap(left, right)` Simply swaps the values in 1 and 8 their respective places.

| Index |  0  |    1   |   2  |   3  |    4   |   5  |   6  |  7  |     8     |
|:-----:|:---:|:------:|:----:|:----:|:------:|:----:|:----:|:---:|:---------:|
| arr   | 22  | **14** | 2001 | 2002 | `1000` | 2000 | 2003 | 10  | **1500**  |

  * Increment left, moving it from 1 to 2.
  * **Decrement right**, moving it from 8 to 7.
  * `while (left <= right)` 2 < 7 == true, we continue iterating:
  * _arr[left] is 2001 < pivot 1000 == false, so left remains 2.
  * _arr[right] == _arr[7] is 10 > pivot 1000 == false, so it remains 7.
  * `2 <= 7` so we swap the values:

| Index |  0  |  1  |   2    |   3  |    4   |   5  |   6  |     7    |   8  |
|:-----:|:---:|:---:|:------:|:----:|:------:|:----:|:----:|:--------:|:----:|
| arr   | 22  | 14  | **10** | 2002 | `1000` | 2000 | 2003 | **2001** | 1500 |

  * Increment left and **decrement** right, left=3, right=6.
  * `while (left <= right)` 3 < 6 == true, we continue iterating:
  * _arr[left] is 2002 < pivot 1000 == false, so left remains 3.
  * However: _arr[right] == _arr[6] == 2003 > pivot 1000 == true, so `right--` becomes 5, 
  * _arr[5] is 2000 < pivot 1000 == true, so `right--` becomes 4.
  * _arr[4] is 1000 < 1000 == false, we move on.
  * `3 <= 4` so we swap the values:
  
| Index |  0  |  1  |  2  |      3     |     4    |   5  |   6  |   7  |   8  |
|:-----:|:---:|:---:|:---:|:----------:|:--------:|:----:|:----:|:----:|:----:|
| arr   | 22  | 14  | 10  | **`1000`** | **2002** | 2000 | 2003 | 2001 | 1500 |

  * Increment left and **decrement** right, left=4, right=3.
  * `left <= right` 4 <= 3 == false
  * `return left` (returns 4, setting int index = 4 at `int index = Partition(left, right)`).

# Back up the stack to Sort
* We test `if (left < index - 1)` where left was passed in and = 0 and index was returned by partition as 4, 0 is less than 3
* We call `Sort( 0, 3 )`

# Sort calls partition
* Sort then calls `int index = Partition(left, right)` and we do it again on our sub set 0-3
  * Our new pivot = arr[(0 + 3) / 2] = 14 (Integer division we round down, so get Index=1)
  * `while (left <= right)` (0 < 3) == true, we continue iterating:
  * _arr[left] == _arr[0] == 22 < pivot 14 == false, so left remains 0.
  * _arr[right] == _arr[3] == 2002 > pivot 14 == true, so `right--`
  * _arr[right] == _arr[2] == 10 > pivot 14 == false, so we move on.
  * `0 <= 2` so we swap the values:

| Index |    0    |   1   |   2    |   3  |   4  |   5  |   6  |   7  |   8  |
|:-----:|:-------:|:-----:|:------:|:----:|:----:|:----:|:----:|:----:|:----:|
| arr   | **10**  | `14`  | **22** | 1000 | 2002 | 2000 | 2003 | 2001 | 1500 |

* Increment left and **decrement** right, left=1, right=1.
* `1 <= 1 == true`, 
* _arr[left] == _arr[1] == 14 < pivot 14 == false so left remains 1.
* _arr[right] == _arr[1] == 14 > pivot 14 == false, so we move on.
*  We go to swap 1 with 1, the swap method recognises this as not required and returns.
* `return left` (returns 2, back to Sort, setting int index = 2).

# Back up the stack to Sort
* We then call `Sort(0, 1)` which returns 1, setting int index = 1;
* `if (left < index - 1)` 0 < 1 - 1 == false, so we move on to the sorting of the RHS for this partition and call Sort(2, 3);
* Pivot becomes 22, right gets decremented once to 2, swap(2,2) is called with no effect. left++ is called resulting in 3 being returned.
* Back in sort 3 > 3 == false and 3 < 3 == false so we return up the stack, and up the stack again.

#The LHS is sorted, now for the RHS.
* Were back to index = 4, right = 8.
* 4 < 8 == true so we now Sort the RHS by calling Sort(4, 8).
* Which results in `int index = Partition(4, 8)`
* Pivot = 2003
* 2002 is in the correct place relative to the pivot so we `left++`.
* 2000 is also in the right place relative to 2003, so we `left++`.
* 2003 is our pivot value, it is not > than itself so we move on.
* 1500 is greater than 2003, it is on the "wrong side" of the pivot so we move on to the swapping.

| Index |  0  |  1  |  2  |   3  |   4  |   5  |     6    |   7  |   8    |
|:-----:|:---:|:---:|:---:|:----:|:----:|:----:|:--------:|:----:|:------:|
| arr   | 10  |  14 |  22 | 1000 | 2002 | 2000 | **1500** | 2001 | `2003` |

* 5 is less than 8, so we swap the values, increment left and **decrement** right.
* 6 is less than 7 so we go again.
* `while (_arr[left] < pivot)` left=7, 2001 < 2003 so `left++` ie left = 8, then 2003 < 2003 so we move on.
* `while (_arr[right] > pivot)` right = 7, 2001 > 2003 == false so we move on.
* `if (left <= right)` 8 <= 7 == false so we move on, `while(8 <= 7)` so we break, and return 8.

# Back up the stack to Sort
* 8 is returned to index, `if(left < index -1)` 4 < 8 - 1 so we call Sort(4, 7).

# Sort calls Partition(4, 7):
* pivot is set to 2000, ie arr[(4 + 7) /2]
* The pointers are set by their while loops to 4 and 6, and the values are swapped.

| Index |  0  |  1  |  2  |   3  |     4    |    5   |     6    |   7  |  8   |
|:-----:|:---:|:---:|:---:|:----:|:--------:|:------:|:--------:|:----:|:----:|
| arr   | 10  |  14 |  22 | 1000 | **1500** | `2000` | **2002** | 2001 | 2003 |

* increment left to 5, **decrement** right to 5.
* `left <= right` == true so we go through again, but the only change is to update left to 6.

# All sorted, but we are not quite finished.

# Back up the stack to Sort
* `if (left < index - 1)` left 4, is less than index - 1 (5).
* We go through the partition proccess again with 4 and 5, resulting in no change, but index being set to 5.
* index = 5, left = 4, right = 5, `if (left < index - 1)` == false, `if (index < right)` == false, so return back up.
* We return up again, and again, and we are finally done.



# Example tests:
{% highlight Csharp %}
[Fact]
public void QuickSortSimpleArrayTest()
{
	int[] expected = { 10, 14, 22, 1000, 1500, 2000, 2001, 2002, 2003 };
	int[] arr = { 22, 1500, 2001, 2002, 1000, 2000, 2003, 10, 14 };

	var quickSort = new QuickSort(arr);

	var result = quickSort.Sort();
	Assert.Equal(expected, result);
}

[Fact]
public void QuickSortLargeRandomArrayTest()
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

	var quickSort = new QuickSort(arr);
	var result = quickSort.Sort();
	Assert.Equal(expected, result);
}
{% endhighlight %}

# Resources:
* [SO/q/164163 Quick Sort Choosing The Pivot](https://stackoverflow.com/questions/164163/quicksort-choosing-the-pivot)
* [Cracking the coding interview 6th edition](http://www.crackingthecodinginterview.com)
* [hacker rank challenges quicksort](https://www.hackerrank.com/challenges/quicksort1/problem)