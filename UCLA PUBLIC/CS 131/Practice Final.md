data Details = Success str | Failure (InvalidInput | Overflow | Underflow)

data Outcome = Details

c1(r:11)
c2(r:22)

bletch(14, bar(9), c2)


bar: 3
foo
bar: 5
10
bar: 5
-10
r: 11
bar: 7
bletch
bar: 9
18
-18
r: 22


b: 
ss:  
a: 
d: 
e: cb


c



fact :: Int -> Int
fact x
	| x <= 0 = 1
	| otherwise x * fact (x - 1)
	
	
binom :: Integer -> Integer -> Integer
binom n k = (fact n) `div` (fact k * fact (n - k))\
\

binomials :: Integer -> [Integer]
binomials n = [binom n k | k <- [0..n]]


pascalsTriangle = [ binomials n | n <- [0..] ]

hasDivisible n = filter (\x -> elem x n) pascalsTriangle