---
layout: post
title:  "Minimum Two Swaps"
date:   2019-12-21 09:00:00 +0000
categories: algorithms
mathjax: true
---

[hackerrank challenges minimum 2 swaps](https://www.hackerrank.com/challenges/minimum-swaps-2)

# Take care with the question:
- Note that the values are distict ("without any duplicates")
- Array consisting of consecutive integers \\(E[1, 2, 3, ..., n]\\) - the important takaway here is the numbers are guaranteed to start from 1, 2, 3 to n.


# At first glance:
- Count the number of out of place elements and subtract 1.

{% highlight Csharp %}
7, 1, 3, 2, 4, 5, 6
1, 2, 3, 4, 5, 6, 7
x  x  ^  x  x  x  x
{% endhighlight %}

ie:

{% highlight Csharp %}
static int minimumSwaps(int[] arr) {

	int[] orig = new int[arr.Length];
	Array.Copy(arr, orig, arr.Length);
	int counter = 0;

	Array.Sort(arr); // OrderBy
	HashSet<int> alreadyCounted = new HashSet<int>();
	for(int i = 0 ; i < arr.Length; i++)
	{
		if(orig[i] != arr[i]){
			// Already in the correct
			counter++;
		}
	}
	return counter -1;
}
{% endhighlight %}

# But
- This doesn't work when the elements have been swapped multiple times, here with a larger test case:

{% highlight Csharp %}
50
2 31 1 38 29 5 44 6 12 18 39 9 48 49 13 11 7 27 14 33 50 21 46 23 15 26 8 47 40 3 32 22 34 42 16 41 24 10 4 28 36 30 37 35 20 17 45 43 25 19
{% endhighlight %}

The above solution gives 48 (two higher than required).

{% highlight Csharp %}
 static int minimumSwaps(int[] arr) {

	int ans = 0;
	var p = new Dictionary<int, int>();

	for(int i = 0; i < arr.Length; i++) {
		p[arr[i]] = i;
	}

	var list = p.Keys.ToList();
	list.Sort();

	var visited = new bool[arr.Length];

	for(int i = 0; i < arr.Length; i++) {
		if(visited[i] || p[list[i]] == i) {
			continue;
		}

		int cycle = 0;
		int j = i;

		while(!visited[j]) {
			visited[j] = true;
			j = p[list[j]];
			cycle++;
		}

		ans += (cycle - 1);
	}

	return ans;
}
{% endhighlight %}
