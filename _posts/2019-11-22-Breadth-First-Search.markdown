---
layout: post
title:  "Breadth First Search"
date:   2019-11-21 09:00:00 +0000
categories: algorithms, searching
---


# Runtime: 

# C#
{% highlight Csharp %}
public Node Search(Node root, string elementToFind)
{
	var queue = new Queue<Node>();
	root.Marked = true;
	queue.Enqueue(root);

	Node elem;
	while ((elem = queue.Dequeue()) != null)
	{
		if (elem.Name == elementToFind)
		{
			return elem;
		}

		foreach (Node n in elem.Children)
		{
			if (!n.Marked)
			{
				n.Marked = true;
				queue.Enqueue(n);
			}
		}
	}

	return null;
}

public class Node
{
	public IEnumerable<Node> Children = new List<Node>();
	public bool Marked;
	public string Name { get; set; }

	public Node(string name)
	{
		Name = name;
	}
}

{% endhighlight %}
[Source: w3 Resource](https://www.w3resource.com/csharp-exercises/searching-and-sorting-algorithm/searching-and-sorting-algorithm-exercise-10.php)
[Shared under create commons licence](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US)

# Example tests:
{% highlight Csharp %}
[Fact]
public void BfsFindRouteBetweenFifteenNodes()
{
	BreadthFirstSearch.Node head = new BreadthFirstSearch.Node("bob");
	head.Children = new List<BreadthFirstSearch.Node>
	{
		new BreadthFirstSearch.Node("Rob"),
		new BreadthFirstSearch.Node("Garry"),
		new BreadthFirstSearch.Node("Helen")
		{
			Children = new List<BreadthFirstSearch.Node>{new BreadthFirstSearch.Node("Andrew") }
		}
	};
	BreadthFirstSearch bfs = new BreadthFirstSearch();
	var result = bfs.Search(head, "Helen");
	var firstChildVal = result.Children.FirstOrDefault().Name;
	Assert.Equal("Andrew", firstChildVal);
}
{% endhighlight %}

# Resources
* [Hacker Rank practice graph theory bfs](https://www.hackerrank.com/challenges/linkedin-practice-graph-theory-bfs)
* [Hacker Rank Pac Man BFS](https://www.hackerrank.com/challenges/pacman-bfs)

# C# Generic version: 
{% highlight Csharp %}
public class BreadthFirstSearch
{
	public Node<T> Search<T>(BreadthFirstSearch.Node<T> root, T elementToFind)
	{
		var queue = new Queue<Node<T>>();
		root.Marked = true;
		queue.Enqueue(root);

		Node<T> elem;
		while ((elem = queue.Dequeue()) != null)
		{
			if (elem.Name.Equals(elementToFind))
			{
				return elem;
			}

			foreach (Node<T> n in elem.Children)
			{
				if (!n.Marked)
				{
					n.Marked = true;
					queue.Enqueue(n);
				}
			}
		}

		return null;
	}

	public class Node<T>
	{
		public IEnumerable<Node<T>> Children = new List<Node<T>>();
		public bool Marked;
		public T Name { get; set; }

		public Node(T name)
		{
			Name = name;
		}
	}
}
{% endhighlight %}