- Made of facts declaring atoms ("things") and functor (function like thing)
```
% Facts:
outgoing(ren).
silly(ren).
parent(alice, bob).
age(ren, 80).
parent(bob, carol).

% Rules:
comedian(P) :- silly(P), outgoing(P).
grandparent(X, Y) :- parent(X,Q), parent(Q,Y).
old_comedian(C) :- comedian(C),
age(C, A), A > 60.

% Recursive rules:
ancestor(X, Z) :- parent(X,Z).
ancestor(X, Z) :- parent(X,Y), ancestor(Y,Z).

% Rules with negation:
serious(X) :- not(silly(X)).

?- outgoing(alice)
true
?- grandparent(brenda, ned)
true
```
- Answer queries:
	1. Add our query to a goal stack
	2. Pop the top goal in our goal stack and match it with each item in our database, from top to bottom
	3. If we find a match, extract the variable mappings, and create a new map with the old+new mappings
	4. Create a new goal stack, copying over existing, unprocessed goals and adding new subgoals
	5. Recursively repeat the process on the new goal stack and the new mappings
- Unification comparision:
	1. If both nodes are functors, then make sure the functors are the same and have the same number of children.
	2. If both nodes hold atoms, make sure the atoms are the same.
	3. If a goal node holds an unmapped variable it will match ANY item in the corresponding node of the fact/rule.
	4. If a fact/rule node holds an unmapped variable it will match ANY item in the corresponding node of the goal.
### Lists
```prolog
is_head_items(X, Y, [X, Y | XS]). % Matches X and Y, 2 items, and XS contains list

append([1,2],[3,4],[1,2,3,4]) yields True
append([1,2],X,[1,2,3,4]) yields X -> [3,4]

sort([4,3,1], [1,3,4]) yields True
sort([4,3,1],X) yields X -> [1,3,4]

permutation([4,3,1], [3,1,4]) yields True


reverse([1,2,3],[3,2,1]) yields True
reverse([1,2,3],X) yields X -> [3,2,1]

member(6, [1,6,4]) yields True
member(X,[1,6,4]) yields X -> 1, X -> 6, and X -> 4

sum_list([4,3,1], 8) yields True
sum_list([4,3,1], Q) yields Q -> 8

[X|Tail] --> cons(X,Tail)
```