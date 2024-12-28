foldl (\acc x -> if x == (last acc) then acc else acc ++ [x]) [(first l)] l
where
	h x [] = [x]
	h x l = if x == last l then acc else [x] ++ l
	
grades x 
	| x > 70 = "A"
	| otherwise

1
p1 (5,  [bonnie, Carey, Brian, Audrey] A)

names =  [bonnie, Carey]

a: bonnie, carey
b: Bonnie Carey Brian
c: Bonnie Carey

d: bonnie, Carey, Brian, Audrey
e:  [bonnie, Carey]


2
a list_with_dups = Node 1 (Node 2 (Node 2 (Node 3 (Node 3 nil))))


dup_rem :: List -> List
dup_rem Nil = Nil
dup_rem (Node val Nil) = (Node val Nil)
dup_rem (Node val (Node innerval l)) = if val == innerval then dup_rem (Node innerval l) else (Node val (dup_rem (Node innerval l)))

since linked list, all nodes before the last changed node are created, so a new 10 node is created pointing to 20

extract2nd l = map (\x -> snd x) l



extract2nd :: [(T, U)] -> [U]
extract2nd l = foldl (\acc x -> acc ++ [snd x]) [] l

filterBy1st :: [(a, b)] -> a -> [(a, b)]
filterBy1st l f = filter (\e -> (fst e) /= f) l

removeElemAtIndex l idx = extract2nd( filterBy1st (zip [0..] l) idx)

dup_rem l = foldr (\acc x -> if x == (head acc) then acc else x ++ acc) [] l


 (a -> b) -> (b -> Int) -> [a] -> Bool
 
  (a -> b) -> ((b -> Int) -> ([a] -> Bool))

[Int] -> Bool

 (Char -> b) -> (b -> Int) -> String -> Bool
[x | x <- [1..a], a `mod` x == 0]

everyOther s = [x | x <- s, y <- [0..], even y]



data BinaryTree = EmptyTree |
	Node Integer BinaryTree BinaryTree
	
Node 4 (Node 2 (Node 1 EmptyTree EmptyTree) (Node 3 EmptyTree EmptyTree)) (Node 7 EmptyTree EmptyTree)

decTree :: BinaryTree -> BinaryTree
decTree EmptyTree = EmptyTree
decTree (Node i l r) = Node (i - 1) (decTree l) (decTree r)


1.

delete :: List -> Integer -> List

delete Nil _ = Nil
delete (Node val next) i = if i == val then delete next else (Node val (delete next val))

```
01 l1 = [33]
02 l2 = [3, l1, 21, 100]
03 l3 = l2
04 l2.append(100)
05 l2[1].remove(55)
06 print(l2) 3, [], 10, 100
07 print(l3) 3, [], 10, 100
08 l3[1].append(33)
09 l3[2] += 11
10 print(l2) 3, [33], 21, 100
11 print(l3) 3, [33], 21, 100
```


substr :: [Char] -> [Char] -> Bool
substr [] [] = True
substr [] _ = True
substr _ [] = False
substr a b = if a == (take (length a) b) then True else substr a (tail b)


1 int int int return int
2 (f(int) -> int) int return int
3 int (f(int) -> int) int return int
4 int int (f(int) -> int) return int

2
5
1 4 7
3 8


((b -> c) -> d) -> e -> f
e -> f

g(x)(y)

def curried_f():
	def f(x):
		def g(y):
			def h(z):
				return x * y + z
			return h
		return g
	return f
	
	
foo :: [Num] -> [Num]
y :: [String] -> [String]
m: [(Bool, String)]
z :: [t1] -> [[t1]] -> [t1]
foo :: t1 -> [t2]


data Graph = Node Integer [Graph]

Node 42 [(Node 5 [])]

O(1) 

sum_of_list :: [Graph] -> Integer
sum_of_list [] = 0
sum_of_list (x: xs) = sum_of_list xs + sum_of_graph x

sum_of_graph :: Graph -> Integer
sum_of_graph (Node i l) = sum_of_list l + i


get_every_nth :: Int -> [t] -> [t]
get_every_nth i l = map (\x -> snd x) (filter (\x -> (fst x) `mod` i == 0) (zip [0..] l))

[(\x -> x ^ k) | k <- [1..] ]

