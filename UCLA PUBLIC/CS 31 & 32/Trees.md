#### Tree requirements:
- One **root** node
- unique path from root node to any other node
#### Tree contents:
- Branches
- Leaf nodes: end nodes with no children
- Interior nodes: nodes that don't end aka has at least one child
- Depth: number of edges traversed to get from root to node
- height of free: maximum depth
#### Traversal types:
- Preorder traversal
- Binary tree specific: inorder
	- Process node in between left and right subtrees
- Postorder traversal
#### Binary tree:
- pointer that is either empty OR a node with data and a left binary tree and a right binary tree
#### Binary Search Tree
- empty or a node with left binary search tree and right binary search tree such that:
	- every value in the left subtree <= this node value
	- every value in the right subtree >= this node value
- Insertions, deletions, and searches are O(log N)
- Searches: compare ==, and if not, compare > / < and go down appropriate path
- Insertions: search until trying to follow nullptr; place item here
- Removing nodes:
	- If one empty node, promote other
	- If no empty nodes, promote immediate next/before item in sort order into that spot
		- Find replacement node by going left and then right as far as possible (opposite also possible)
		- Once node found, move node up and attach that node's left node pointer to the parent's right pointer ("shifting" it up)
	- NOTE: either flip a coin or alternate which side you pick the promoted node from to prevent excess tree imbalancing
### C++ related tree types
- Set:
	- Sorted using binary search tree
	- All items must be unique
	- Can loop through items using iterators in increasing order
- Multiset:
	- Set but does allow duplicates
```
#include <set>
set<int> s;
s.insert(10);
s.insert(10);
s.insert(20);
cout << s.size(); // 2
if (s.find(20) == s.end) {
	cout << "20 is not in the set";
}
// iterators in alphabetical order

multiset<int> ms;
ms.insert(10);
ms.insert(10);
ms.insert(20);
cout << ms.size(); // 3
if (ms.find(20) == ms.end) {
	cout << "20 is not in the set";
}
```
