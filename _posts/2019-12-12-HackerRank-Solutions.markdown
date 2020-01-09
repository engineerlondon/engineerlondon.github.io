---
layout: post
title:  "Hacker Rank Solutions"
date:   2019-12-21 09:00:00 +0000
categories: algorithms
mathjax: true
---

# Singly Linked List Node

{% highlight Csharp %}
SinglyLinkedListNode {
         int data;
         SinglyLinkedListNode next;
     }
 {% endhighlight %}

# Insert a node at a specific position in a linked list

{% highlight Csharp %}
static SinglyLinkedListNode insertNodeAtPosition(SinglyLinkedListNode head, int data, int position) {
	if(head != null){
		int counter = 1;
		var temp = head;
		// node1 -> node2.next = AddedNode, AddedNode.next = -> node3 -> node -> node
		while(temp != null && counter < position){
			temp = temp.next;
			counter++;
		}
		var holder = temp.next;
		temp.next = new SinglyLinkedListNode(data);
		newNode.next = holder;

	}
	else{
		head = new SinglyLinkedListNode(data);
	}
	return head;
}
{% endhighlight %}

# Delete a Node
// ToDo come back to this and make it neater, it seems overly complicated.

{% highlight Csharp %}
static SinglyLinkedListNode deleteNode(SinglyLinkedListNode head, int position)
{
    if(position == 0 && head != null)
        head = head.next;
    else if(head != null){
        int counter = 1;
        var temp = head;
        while(temp != null && counter < position){
            temp = temp.next;
            counter++;
        }

        if(temp.next != null && temp.next.next != null)
            temp.next = temp.next.next;
        else
            temp.next = null;
    }
    return head;
}
{% endhighlight %}
- [https://www.hackerrank.com/challenges/delete-a-node-from-a-linked-list](https://www.hackerrank.com/challenges/delete-a-node-from-a-linked-list)

# Print in Reverse

{% highlight Csharp %}
static SinglyLinkedListNode reverse(SinglyLinkedListNode head) {
	SinglyLinkedListNode rev = null;

	if(head == null || head.next == null){
		return head;
	}

	rev = reverse(head.next);

	head.next.next = head;
	head.next = null;
	return rev;
}
{% endhighlight %}
- [https://www.hackerrank.com/challenges/print-the-elements-of-a-linked-list-in-reverse/problem](https://www.hackerrank.com/challenges/print-the-elements-of-a-linked-list-in-reverse/problem)
- [https://www.educative.io/courses/coderust-hacking-the-coding-interview/lq2j](https://www.educative.io/courses/coderust-hacking-the-coding-interview/lq2j)

# Compare two linked lists

{% highlight Csharp %}
static bool CompareLists(SinglyLinkedListNode head1, SinglyLinkedListNode head2) {
	bool result = true;
	while(head1 != null || head2 != null){
		if(head1 == null || head2 == null || head1.data != head2.data)
		{
			result = false;
		}
		if(head1 != null)
			head1 = head1.next;
		if(head2 != null)
			head2 = head2.next;
	}
	return result;
}
{% endhighlight %}

# Merge two sorted linked lists

{% highlight Csharp %}
static SinglyLinkedListNode mergeLists(SinglyLinkedListNode head1, SinglyLinkedListNode head2) {
SinglyLinkedListNode resultHead;
	if(head1.data > head2.data)
	{
		resultHead = new SinglyLinkedListNode(head2.data);
		head2 = head2.next;
	}
	else
	{
		resultHead = new SinglyLinkedListNode(head1.data);
		head1 = head1.next;
	}
	var result = resultHead;
	while(head1 != null || head2 != null)
	{
		if(head1 != null && head2 != null){
			if(head1.data > head2.data){
				result.next = new SinglyLinkedListNode(head2.data);
				head2 = head2.next;
			}
			else{
				result.next = new SinglyLinkedListNode(head1.data);
				head1 = head1.next;
			}
		}
		else if(head1 != null){
			result.next = new SinglyLinkedListNode(head1.data);
			head1 = head1.next;
		}
		else if(head2 != null){
			result.next = new SinglyLinkedListNode(head2.data);
			head2 = head2.next;
		}
		result = result.next;
	}
	return resultHead;
}
{% endhighlight %}

- [https://www.hackerrank.com/challenges/merge-two-sorted-linked-lists](https://www.hackerrank.com/challenges/merge-two-sorted-linked-lists)

# Get Node Value

{% highlight Csharp %}
static int getNode(SinglyLinkedListNode head, int positionFromTail) {
	int totalCounter = 0;
	Dictionary<int,int> tracker = new Dictionary<int,int>(); // bad memory usage.

	tracker.Add(totalCounter, head.data);

	while(head.next != null){
		head = head.next;
		tracker.Add(++totalCounter, head.data);
	}

	return tracker[totalCounter - positionFromTail];
}
{% endhighlight %}
- [https://www.hackerrank.com/challenges/get-the-value-of-the-node-at-a-specific-position-from-the-tail](https://www.hackerrank.com/challenges/get-the-value-of-the-node-at-a-specific-position-from-the-tail)

# Forming a Magic Square
- [https://www.hackerrank.com/challenges/magic-square-forming/problem](https://www.hackerrank.com/challenges/magic-square-forming/problem)

{% highlight Csharp %}
static int formingMagicSquare(int[][] arr) {
        var magicSquares = new List<int[][]>
		{
			new []{ new[] { 8, 1, 6 }, new[] { 3, 5, 7 }, new[] { 4, 9, 2 } },
			new [] { new[] { 6, 1, 8 }, new[] { 7, 5, 3 }, new[] { 2, 9, 4 } },
			new [] { new[] { 4, 9, 2 }, new[] { 3, 5, 7 }, new[] { 8, 1, 6 } },
			new [] { new[] { 2, 9, 4 }, new[] { 7, 5, 3 }, new[] { 6, 1, 8 } },
			new [] { new[] { 8, 3, 4 }, new[] { 1, 5, 9 }, new[] { 6, 7, 2 } },
			new [] { new[] { 4, 3, 8 }, new[] { 9, 5, 1 }, new[] { 2, 7, 6 } },
			new [] { new[] { 6, 7, 2 }, new[] { 1, 5, 9 }, new[] { 8, 3, 4 } },
			new [] { new[] { 2, 7, 6 }, new[] { 9, 5, 1 }, new[] { 4, 3, 8 } },
		};
        int smallest = int.MaxValue;
        foreach(var square in magicSquares){
            int diff = 0;
            for(int i = 0; i < 3; i++){
                for(int j = 0; j < 3; j++){
                    diff += Math.Abs(square[i][j] - arr[i][j]);
                }
            }
            if(diff < smallest)
                smallest = diff;
        }

        return smallest;
    }
{% endhighlight %}
