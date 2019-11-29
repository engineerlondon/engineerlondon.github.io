---
layout: post
title:  "Breadth First Search"
date:   2019-11-21 09:00:00 +0000
categories: algorithms, searching
mathjax: true
---
Graphs and Trees are a versatile way of representing the real world, described in terms of nodes (ie the entities of interest) and edges (ie the links between the nodes), examples include:
* A social network of relationships
* Travel routing; flights between airports where the airports are the nodes and the routes are the edges. Or towns are the nodes and roads are the edges. Or pubs and rivers are the edges
* Internet connections to properties where properties are nodes and telephone lines are the edges.
The multi dimensional nature of graph structures enables reduces the complexity of creating a digital representation of relationships. It is important to note that data in a graph structure is not necessarily connected. 
Being able to traverse graphs is valuable, we can find the shortest path between locations, or the number of friends connecting two indirviduals on a social network.
Breadth First Search (BFS) and Depth First Search (DFS) are two core tools that enable the finding of a specific connected node in a graph starting from a known node.
* BFS can be used to calculate the shortest distance, and route from A to B.
# DFS vs BFS
There are a number of situations where BFS is more appropriate than DFS and vice versa, the context of the interaction is crucial for choosing the right tool.
* When a node should be close to the start point, eg Social network, the friend that a person is searching for is likely to be a small number of nodes from them. City I want to route to is likely to be the shorter distance, eg: We start in London (UK) and want to reach Glasgow I want the map to offer Glasgow, Scotland (UK) before Glasgow, Montana (USA). - BFS
* If a graph has "infinite" relationships for example the internet with its (~340 undecillion possible addresses) and we want to send packets from A to B, we use a specialised version of BFS (Dijkstra's algorithm) which will take into account the cost of a route, we also make use of Time to live (TTL) or hop limit in the system to avoid requests continuig forever.
 

# Runtime: Worst case: \\(O(V + E)\\)
* Where E is the count edges and V is the count of vertices.
* Where the graph uses an adjacency list structure
* Aside: adjacency list has a space complexity of \\(O(V + E)\\).
* "An adjacency list is an array A of separate lists. Each element of the array Ai is a list, which contains all the vertices that are adjacent to vertex i." - [Tutorial on Graph Representation](https://www.hackerearth.com/practice/algorithms/graphs/graph-representation/tutorial)
* "An adjacency list representation for a graph associates each vertex in the graph with the collection of its neighbouring vertices or edges" - [Wikipedia Adjacency List](https://en.wikipedia.org/wiki/Adjacency_list)

* [Stack Overflow - BFS and DFS time complexity](https://stackoverflow.com/questions/11468621/why-is-the-time-complexity-of-both-dfs-and-bfs-o-v-e)

# C# BFS finds a node with name = elementToFind.
* The queue is the core of BFS, it allows us to visit all adjacent nodes before moving to the next level.

{% highlight Csharp %}
public class SimpleNode
{
	public IEnumerable<SimpleNode> Children = new List<SimpleNode>();
	public bool Marked;
	public string Name { get; }

	public SimpleNode(string name)
	{
		Name = name;
	}
}

public SimpleNode Search(SimpleNode root, string elementToFind)
{
	var queue = new Queue<SimpleNode>();
	root.Marked = true;
	queue.Enqueue(root);

	while (queue.Count > 0)
	{
		var elem = queue.Dequeue();
		if (elem.Name == elementToFind)
		{
			return elem;
		}

		foreach (SimpleNode n in elem.Children)
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
{% endhighlight %}

# Quick Walk-through
* We add the head of our node to the queue
* We go into the while loop `while (queue.Count > 0)` and de-queue the head node
* We check if the value of the node is the one we are looking for
* We then iterate the children of the head node
* For each child we check if we have already visited it `if (!n.Marked)`
* If not visited we now mark it as visited and enqueue the node so that it's value and children are checked
* If we never find a result we return null.

# Example tests:
{% highlight Csharp %}
[Fact]
public void StringBfsSearchTest()
{
	var head = new StringBreadthFirstSearch.SimpleNode("bob")
	{
		Children = new List<StringBreadthFirstSearch.SimpleNode>
		{
			new StringBreadthFirstSearch.SimpleNode("Rob"),
			new StringBreadthFirstSearch.SimpleNode("Garry"),
			new StringBreadthFirstSearch.SimpleNode("Helen")
			{
				Children = new List<StringBreadthFirstSearch.SimpleNode>
					{new StringBreadthFirstSearch.SimpleNode("Andrew")}
			}
		}
	};

	StringBreadthFirstSearch bfs = new StringBreadthFirstSearch();
	var result = bfs.Search(head, "Helen");
	var firstChildVal = result.Children.FirstOrDefault().Name;
	Assert.Equal("Andrew", firstChildVal);
}
{% endhighlight %}


# C# Generic version:
Introducing support for data types other than string, by creating a generic node class:
{% highlight Csharp %}

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

public Node<T> Search<T>(Node<T> root, T elementToFind)
{
	var queue = new Queue<Node<T>>();
	root.Marked = true;
	queue.Enqueue(root);

	while (queue.Count > 0)
	{
		var elem = queue.Dequeue();
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

{% endhighlight %}




# Resources
* [Hacker Rank practice graph theory bfs](https://www.hackerrank.com/challenges/linkedin-practice-graph-theory-bfs)
* [Hacker Rank Pac Man BFS](https://www.hackerrank.com/challenges/pacman-bfs)
* [Graphs - part of a Microsoft serries "Extensive Examination of Data Structures Using C#"](https://docs.microsoft.com/en-us/previous-versions/ms379574(v=vs.80))
