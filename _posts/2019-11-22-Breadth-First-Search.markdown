---
layout: post
title:  "Breadth First Search"
date:   2019-11-21 09:00:00 +0000
categories: algorithms, searching
mathjax: true
---
Graphs and Trees are a versatile way of representing the real world, described in terms of nodes (ie the entities of interest) and edges (ie the links between the nodes), examples include:
* A social network of relationships.
* Travel routing:
	* Flights where airports are nodes and flight routes are edges.
	* Towns are nodes and roads the edges.
	* Pubs and rivers are edges.
* Internet connections to properties where properties are nodes and telephone lines are edges.

The multi dimensional nature of graph structures reduces the complexity of creating a digital representation of relationships.
It is important to note that data in a graph structure is not necessarily connected.

Consequently being able to traverse graphs is valuable, we can find the shortest path between locations or the number of friends connecting two individuals on a social network.
* Breadth First Search (BFS) and Depth First Search (DFS) are two core tools that enable the finding of a specific connected node in a graph starting from a known node.
* BFS can be used to efficiently evaluate the shortest path.
* In the same way BFS can be used to find the shortest route from A to B.

# DFS vs BFS
There are a number of situations where BFS is more appropriate than DFS and vice versa, the context of the interaction is crucial for choosing the right tool.
* When a node should be close to the start point, eg Social network, the friend that a person is searching for is likely to be a small number of nodes from them. City I want to route to is likely to be the shorter distance, eg: We start in London (UK) and want to reach Glasgow I want the map to offer Glasgow, Scotland (UK) before Glasgow, Montana (USA). - BFS
* If a graph has "infinite" relationships for example the internet with its (~340 undecillion possible addresses) and we want to send packets from A to B, we use a specialised version of BFS (Dijkstra's algorithm) which will take into account the cost of a route, we also make use of Time to live (TTL) or hop limit in the system to avoid requests continuing forever.
* If solutions are frequent but located deep in the tree BFS will likely take longer and require more memory than DFS eg: given a Graph representing the geographical location of all towns and villages in Ireland, find a town or village located on the Wild Atlantic way, start in Dublin.

# Runtime: Worst case: \\(O(V + E)\\)
* Where E is the count edges and V is the count of vertices (nodes).
* Where the graph uses an adjacency list structure
* [Stack Overflow - BFS and DFS time complexity](https://stackoverflow.com/questions/11468621/why-is-the-time-complexity-of-both-dfs-and-bfs-o-v-e)

# Memory: Worst case \\(O(V)\\)
* Where V is the set of vertices / count of all nodes, since in the worst case we could have a single head node with all other nodes immediately attached, as you will see below, we will enqueue all of the child nodes before starting to dequeue them. As the graph gets larger this could become impractical.

# Adjacency Graph space complexity \\(O(V + E)\\)
* It is sometimes useful to consider the cost of the actual graph since we might want to fit both the graph and the data structure required to carry out the search on one machine.
* "An adjacency list is an array A of separate lists. Each element of the array Ai is a list, which contains all the vertices that are adjacent to vertex i." - [Tutorial on Graph Representation](https://www.hackerearth.com/practice/algorithms/graphs/graph-representation/tutorial)
* "An adjacency list representation for a graph associates each vertex in the graph with the collection of its neighbouring vertices or edges" - [Wikipedia Adjacency List](https://en.wikipedia.org/wiki/Adjacency_list)

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
		if (elem.Name.Equals(elementToFind))
			return elem;


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
* We mark the head node as visited.
* We add the head of our node to the queue.
* We go into the while loop `while (queue.Count > 0)` and de-queue the head node.
* We check if the value of the node is the one we are looking for.
* We then iterate the children of the head node.
* For each child we check if we have already visited it `if (!n.Marked)`.
* If not visited we now mark it as visited and enqueue the node so that it's value and children are checked.
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
Introducing support for data types other than string, using a generic node class:
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
			return elem;

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

# BFS Finding distance to a node


# C# Tests:
{% highlight Csharp %}
public class BfsShortestPathTests
{
	[Fact]
	public void BfsCountShortestPath()
	{
		var alex = new BfsNode.Node<string>("Alex");
		var rob = new BfsNode.Node<string>("Rob");
		var garry = new BfsNode.Node<string>("Garry");

		// Khalid is friends with alex, rob and garry
		var khalid = new BfsNode.Node<string>("Khalid")
		{
			Children = new List<BfsNode.Node<string>>
				{alex, rob, garry}
		};
		// Andrew if friends with Alex and Khalid
		var andrew = new BfsNode.Node<string>("Andrew")
		{
			Children = new List<BfsNode.Node<string>>
				{khalid, alex}
		};
		// Helen is friends with Andrew
		var helen = new BfsNode.Node<string>("Helen")
		{
			Children = new List<BfsNode.Node<string>> { andrew }
		};
		// Bob is friends with Helen and Khalid
		// Shortest path to Alex = Bob (0) -> Khalid (1) -> Alex (2 connections away).
		// Longer path to Alex = Bob -> Helen -> Andrew -> Alex (3 connections away).
		// Even longer path Alex = Bob -> Helen -> Andrew -> Khalid -> Alex (4 connections away).
		BfsNode.Node<string> head = new BfsNode.Node<string>("Bob")
		{
			Children = new List<BfsNode.Node<string>>
			{ helen, khalid}
		};

		var bfs = new BfsCountShortestPath();
		var result = bfs.Search(head, "Alex");
		int expected = 2;
		Assert.Equal(expected, result);
	}

	[Fact]
	public void BfsCountShortestNoChildNodesNoMatchPath()
	{
		BfsNode.Node<string> head = new BfsNode.Node<string>("bob");

		var bfs = new BfsCountShortestPath();
		var result = bfs.Search(head, "Alex");
		int expected = -1;
		Assert.Equal(expected, result);
	}

	[Fact]
	public void BfsCountShortestNoChildNodesMatchPath()
	{
		BfsNode.Node<string> head = new BfsNode.Node<string>("bob");

		var bfs = new BfsCountShortestPath();
		var result = bfs.Search(head, "bob");
		int expected = 0;
		Assert.Equal(expected, result);
	}
}
{% endhighlight %}

# C# Solution - BFS find shortest Path
* The addition of a Dictionary (equivalent of a Java HashMap) increases our memory requirements by \\(O(n)\\).

{% highlight Csharp %}
public int Search<T>(BfsNode.Node<T> root, T elementToFind)
{
	var dist = new Dictionary<T, int>();
	var queue = new Queue<BfsNode.Node<T>>();
	root.Marked = true;
	queue.Enqueue(root);
	dist.Add(root.Name, 0);

	while (queue.Count > 0)
	{
		var elem = queue.Dequeue();

		if (elem.Name.Equals(elementToFind))
			return dist[elem.Name];

		foreach (BfsNode.Node<T> n in elem.Children)
		{
			if (!n.Marked)
			{
				n.Marked = true;
				queue.Enqueue(n);
				if (!dist.ContainsKey(n.Name))
					dist.Add(n.Name, dist[elem.Name] + 1);
			}
		}
	}

	return -1;
}
{% endhighlight %}

# Walk-through of changes:
* We have added a Dictionary **dist** to track the number of hops for a given node.
* We add the head node distance as zero.
* While the queue is not empty, we check if the element we are looking for is the element we have taken off the queue, this avoids unnecessary work continuing after a solution has been found. If this node's name matches the one we are searching for, return its distance value from the Dictionary using the key `dist[elem.Name]`.
* Else we go through the child nodes adding them to the queue, and in the process we check if a distance has not already been added `if (!dist.ContainsKey(n.Name))` (it will be the shorter or equal distance), if not already added we add the distance using the name as its key and the value = current element's distance + 1  `dist.Add(n.Name, dist[elem.Name] + 1)`.


# Resources
* [Hacker Rank practice graph theory bfs](https://www.hackerrank.com/challenges/linkedin-practice-graph-theory-bfs)
* [Hacker Rank Pac Man BFS](https://www.hackerrank.com/challenges/pacman-bfs)
* [Graphs - part of a Microsoft serries "Extensive Examination of Data Structures Using C#"](https://docs.microsoft.com/en-us/previous-versions/ms379574(v=vs.80))
