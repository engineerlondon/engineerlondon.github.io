---
layout: post
title:  "Fibonacci Recursion and Iterative"
date:   2019-12-18 09:00:00 +0000
categories: algorithms
mathjax: true
---


# Printing Fibonacci sequence up to n.

- Below I have re-produced a number of common approaches to calculating Fib(n);
- Then to print the sequence of Fib(n) we can iterate over all numbers between 0 and numOfFibElements.

# Without caching

## Runtime of Fibonacci(int n): \\(O(n^2)\\)

{% highlight Csharp %}
private static void Main()
{
    var numOfFibElements = 15;
    var results = new List<int>();
    for (int i = 0; i <= numOfFibElements; i++)
        results.Add(Fibonacci(i));

    Console.WriteLine(string.Join(", ", results));
}

public static int Fibonacci(int n)
{
    if (n <= 1)
        return n;

    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
{% endhighlight %}

- So if we pass in 5:
- We get a binary call tree result where each child node is either n-1 or n-2 until we reach 0 or 1.
- We sum these values to get our total result, in this case we get 1, 0, 1, 1, 0, 1, 0, 1 on the leaves, ie 5.
- We can see when doing this that we get repeated nodes, 3 twice and 2 three times.
- Storing a cache for these values we can reduce our space requirements (fewer calls on the stack), and improve performance.

n = 15 produces:
- 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610.

# Adding a cache:

## Runtime \\(O(n)\\), Space \\(O(n)\\)

{% highlight Csharp %}
public static int Fibonacci(int n, int[] cache)
{
    if (n <= 1)
        return n;

    if (cache[n] == 0)
        cache[n] = Fibonacci(n - 1, cache) + Fibonacci(n - 2, cache);

    return cache[n];
}
{% endhighlight %}

# Iterative Fibonacci
## Runtime \\(O(n)\\), Space / Memory \\(O(1)\\)

{% highlight Csharp %}
public static long Fibonacci(long n)
{
    long a = 0;
    long b = 1;
    for (long i = 0; i < n; i++)
    {
        long temp = a;
        a = b;
        b = temp + b;
    }

    return a;
}
{% endhighlight %}

Resources:
- [https://www.dotnetperls.com/fibonacci](https://www.dotnetperls.com/fibonacci)
- [https://www.javatpoint.com/python-display-fibonacci-sequence-recursion](https://www.javatpoint.com/python-display-fibonacci-sequence-recursion)
