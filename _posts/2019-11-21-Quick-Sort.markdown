---
layout: post
title:  "Quick Sort"
date:   2019-11-21 09:00:00 +0000
categories: algorithms
---
More efficient than iterating through the array, except for the special case where the array is **already** sorted.

# Runtime: *O (n Log n)*
Where n is the number of elements to sort.

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

We want :

| Index |  0  |   1  |   2  |   3  |   4  |   5  |   6  |  7   |  8   |
|:-----:|:---:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| arr   |  10 |  14  |  22  | 1000 | 1500 | 2000 | 2001 | 2002 | 2003 |

We start with:

| Index |  0  |   1  |   2  |   3  |    4   |   5  |   6  |  7  |  8  |
|:-----:|:---:|:----:|:----:|:----:|:------:|:----:|:----:|:---:|:---:|
| arr   | 22  | 1500 | 2001 | 2002 | `1000` | 2000 | 2003 | 10  | 14  |

# Partition:
* We pick the middle of the array for our partition as this reduces the risk of the worst case performance O(n^2) ie: `int left = 0, right = arr.Length -1`.
* `int pivot = _arr[(left + right) / 2];` Eg: *pivot = 1000*.
* `while (left <= right)` Check that the pointers that we will use to choose which elements to swap are still left <= right. Quick Sort will work with values around the current pivot until this is not longer satisfied, then partition again, selecting a new pivot in the proccess.
* `while (_arr[left] < pivot) left++;` - Find a value on the LHS of the pivot that should be on the RHS. ie greater than 1000 for our first pass, the first is left = 1, **arr[1] = 1500**.
* `while (_arr[right] > pivot) right--;` - Does the same for the value on the RHS that should be on the LHS. ie take the first value that is less than 1000, in this case **arr[8] = 14**.
* `if (left <= right)` - We only swap the values chosen if the index in left, is equal to or lower than the index in right. This is the same test as the earlier `while (left <= right)`   
* `Swap(left, right);` - Simply swaps the values in 1 and 8 their respective places.

| Index |  0  |    1   |   2  |   3  |    4   |   5  |   6  |  7  |     8     |
|:-----:|:---:|:------:|:----:|:----:|:------:|:----:|:----:|:---:|:---------:|
| arr   | 22  | **14** | 2001 | 2002 | `1000` | 2000 | 2003 | 10  | **1500**  |

  * Increment left, moving it from 1 to 2.
  * **Decrement right**, moving it from 8 to 7.
  * `while (left <= right)` 2 < 7 == true, we continue iterating:
  * _arr[left] is 2001 < pivot 1000, so left remains 2.
  * _arr[right] is 10 < pivot 1000, so it remains 7.
  * `2 <= 7` so we swap the values:

| Index |  0  |  1  |   2    |   3  |    4   |   5  |   6  |     7    |   8  |
|:-----:|:---:|:---:|:------:|:----:|:------:|:----:|:----:|:--------:|:----:|
| arr   | 22  | 14  | **10** | 2002 | `1000` | 2000 | 2003 | **2001** | 1500 |

  * Increment left and **decrement** right, left=3, right=6.
  * `while (left <= right)` 3 < 6 == true, we continue iterating:
  * _arr[left] is 2002 < pivot 1000 == false, so left remains 3.
  * However: _arr[right] == _arr[6] == 2003 < pivot 1000 == true, so `right--` becomes 5, 
  * _arr[5] is 2000 < pivot 1000 == true, so `right--` becomes 4.
  * _arr[4] is 1000 < 1000 == false, we move on.
  * `3 <= 4` so we swap the values:
  
| Index |  0  |  1  |  2  |      3     |     4    |   5  |   6  |   7  |   8  |
|:-----:|:---:|:---:|:---:|:----------:|:--------:|:----:|:----:|:----:|:----:|
| arr   | 22  | 14  | 10  | **`1000`** | **2002** | 2000 | 2003 | 2001 | 1500 |

  * Increment left and **decrement** right, left=4, right=3.
  * `left <= right` 4 <= 3 == false
  * `return left;` (returns 4, setting int index = 4 at `int index = Partition(left, right);`).

# Back in Sort
* We test `if (left < index - 1)` where left was passed in and = 0 and index was returned by partition as 4, 0 is less than 3
* so we call `Sort( 0, 3 )`
* Sort then calls `int index = Partition(left, right);` and we do it again on our sub set 0-3
| Index |  0  |  1  |   2    |   3  |    4   |   5  |   6  |     7    |   8  |
|:-----:|:---:|:---:|:------:|:----:|:------:|:----:|:----:|:--------:|:----:|
| arr   | 22  | 14  | **10** | 2002 | `1000` | 2000 | 2003 | **2001** | 1500 |

  * `while (left <= right)` 0 < 3 == true, we continue iterating:
  * _arr[left] is 2002 < pivot 1000, so left remains 3.
  * However: _arr[right] == _arr[6] == 2003 < pivot 1000 == true, so `right--` becomes 5, 
  * _arr[5] is 2000 < pivot 1000 == true, so `right--` becomes 4.
  * _arr[4] is 1000 < 1000 == false, we move on.
  * `3 <= 4` so we swap the values:


# Resources:
[SO/q/164163/ Quick Sort Choosing The Pivot](https://stackoverflow.com/questions/164163/quicksort-choosing-the-pivot) 