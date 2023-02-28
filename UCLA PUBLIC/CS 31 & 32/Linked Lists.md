- Notes when making linked lists:
    - DRAW PICTURES to make sure code does the proper thing
		- Trace through algorithms working with linked list at the front, middle, and back of the list
		- Also check for empty and one-element list
	- whenever writing p->SOMETHING, make sure:
		- p is not uninitialized
		- p is not nullptr
	- PROPERLY make for loops (don't write ++ by accident!)
- Doubly linked list: each node points to next AND previous
- Singly linked list: shown below, only has next
- Circually linked list: end points to beginning, beginning to end if doubly linked
	- Eliminates issues with start/end special cases
- Dummy node: head points to a dummy node that is uninitialized and not considered part of the linked list
	- Bypasses issues with zero and one node lists: only head (no separate tail) and always has one node
```cpp

struct Node {
	int value;
	Node* next;
}
Node* head = {something};

// Print all values
for (Node* p = head; p != nullptr; p = p->next) {
	cout >> p->value << endl;
}

// Point p at first node that has value 18
Node* p;
for (p = head; p != nullptr && p -> value != 18; p=p->next) {
	;
}

// Insert a 54 after the 18 in the list, if present
if (p != nullptr) {
	Node* newNode = new Node;
	// Make changes to newly created node FIRST since they don't already have important data
	newNode->value = 54;
	newNode->next = p->next;
	p->next = newNode;
}

// Remove node after 18
if (p != nullptr) {
	Node* delNode = p->next;
	p->next = delNode->next;
	delete delNode;
}
```

