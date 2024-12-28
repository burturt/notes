FP: all functions take one argument and return a value, functions can be stored in variables and passed as arguments, variables are immutable, and functions are pure
- Pure Function: given an input x, always returns the same output, and does not rely on or modify external data that may change from call to call
	- easier reasoning, debugging, testing, allows parallelism/optimizations, and formally analyze programs easier
- Interpreter: `ghci`, `:load hello.hs`, `:r`
## Types
- Bool
- Char
- Int (Integer arbitrary precision)
- Float/Double
- String = [Char]
- Type classes: set of operations
	-  `Eq`: /= and == possible
	-  `Ord`: Eq + can be ordered
	-  `Num`: +, *, abs
	-  `Fractional`: Rational number

```hs
circArea :: Double -> Double
circArea	radius = 3.14 * radius^2


main :: IO() -- If function not IO type, can't do IO
main = do
	let result = circArea 5
	putStrLn("Result: " ++ show result)
```
- Declaration: `fname :: arg1Type -> arg2Type -> arg3Type -> retType`
- Statement: `fname arg1 arg2 arg3 = expression`
	- multi line expressions allowed with indentation
- Numbers: Int, Double, etc
	- negative numbers must have parenthesis around, e.g. `(-1.0)`
- operators: `+`, `-`, `*`, `/` for double, \`div\` for int division, \`mod\` for mod
- Inline def in ghcr: `:{ :}`
- Has type inference: rarely need to actually specify types explicitly
	- still strictly typed
- prefix supported:
	- surround operator with `()`
	- `(*) 3 x^2` --> `3 * x^2`
	- `5 + 6/2` --> `(+) 5 ((/) 6 2)`
	- `a == b` --> `(==) a b`
	- `x || y` --> `(||) x y`
- Infix supported:
	- ARG1 \`OP\` ARG2
	- 5 \`div\` 2
- Evaluates left-to-right
	- `f g 2` tries `(f(g)2)` 
- Calls functions before evaluating operators
	- `f 3 * 10` --> `f(3) * 10`

| Math        | Haskell   |
|-------------|-----------|
| f(x)        | f x       |
| f(x,y)      | f x y     |
| f(g(x))     | f (g x)   |
| f(x, g(y))  | f x (g y) |
| f(x) * g(y) | f x * g y |

- if statement: `if cond then val1 else val2`
	- must always contain value, so must always have both then and else
	- `==` or `/=` for not equals
- guards:
```hs
ee score prof
	| score < 50 = "You failed"
	| score > 50 = "You passed"
	| otherwise = "you got exactly 50 somehow"
```
- let expression: define local variables ("local bindings")
```hs
get_final_grade points = 
	let
		grade = 4.0 * points
		ec_tot = grade + 0.1
	in
	-- Must be one expression only
		if ec_tot > 8
		then "amazing!"
		else "not amazing"
		
-- Same except bindings at end
get_final_grade_2 points = 
	if ec_tot > 8
		then "amazing!"
		else "not amazing"
	where
		grade = 4.0 * points
		ec_tot = grade + 0.1
```
- If defining var for single expression, use either. If defining across multiple expressions (e.g. guards), use where
- Can use to define nested functions
- Tuples: `("a", b)`
	- Can call `fst` or `snd` to get first/second on 2-field tuple
	- `(Double, String) -> (Bool, Int)` example
- Lists:
	- `[1, 2, 3]` to define
	- `length l` to get length
	- `head l` to get first value, returns type
	- `last l` to get last value
	- `tail l` to get all except first value, returns list
	- `null` returns if list empty
	- `take N l` returns first N elements
	- `drop N l` returns list minus first N elements
	- `sum l` or `product l`
	- `or l` or `and l` takes boolean or/and of list
	- `zip l1 l2` returns list of pairs, ignoring excess data
	- `l !! 2` gets 2nd zero-indexed element
	- `elem e l` checks if e is in l
		- Basically python `in` operator
- Ranges
	- `[1..3] == [1,2,3]`
	- `[1,3..10] == [1,3,5,7,9]`
	- `[42..]` infinite list
	- `cycle [1,3,5]` infinite list, cycling 1,3,5,1,3,5,1,...
- List operators
	- `l1 ++ l2`: list concatenation (same type)
	- `e : l`: cons op: prepend e to list
### List Comprehensions
- Generators: Input list(s) 
- Guards: filters on input lists
- Transformation applied before added to output list
- `output = [ (f x) | x <- input_list, (guard x), (guard2 x), ...]`
	- All guard rules must pass
- Can do multiple lists as input:
- `output = [(f x y) | x <- l1, y <- l2, (guard x), (guard2 y)]`
	- Processes cross product
	- Loops x0 y0, x0 y1, x0 y2, ..., x1 y1, ...
```hs
a = [x | x <- [1..a], a `mod` x == 0]

right_triangle_tuples = [ (a,b,c) | 
     a <- [1..10], b <- [a..10], c <- [b..10],
     a^2+b^2==c^2 ] 
	 
```
### Pattern Matching
![Screenshot 2024-10-14 at 3.30.27 PM.png](../../_resources/Screenshot%202024-10-14%20at%203.30.27%20PM.png)
### Type Variables
- Use `t` or `t1`, `t2`, etc for generic types
- `Ord a =>`: type a is an orderable type (comparable with < and >)
- `Fractional a =>` must be rational or floating point
## First-class and Higher-order Functions
- First class: function is treated like any other data
	- Can be stored in vars, passed to/returned by functions, stored in data structures
- Higher order function: function that accepts or returns a function
	- Put function arguments in parenthesis
```hs
praise :: String -> String
praise name = name ++ "is dank!"

talk_to :: String -> (String -> String) -> String
talk_to name talk_func
	| name == "Carey" = "No comment."
	| otherwise = (talk_func name)
	
talk_to "Devan" praise
```
- Example return function
```hs
get_pickup_func :: Int -> (String -> String)
get_pickup_func born
  | born >= 1997 && born <= 2012 = pickup_genz
  | otherwise = pickup_other
 where
   pickup_genz name = name ++ ", you've got steez!"
   pickup_other name = name ++ ", you've got style!" 
```
#### higher-order utility list processing functions
- Mappers: one-to-one list transformation using **transform** function
- Filters: use **predicate** function to remove some items
- Reducers: collapses list into single output value using **reducer** function
```hs
cube :: Double -> Double
cube x = x^3

map cube [1,2,3] -- [1,4,9]

map :: (a -> b) -> [a] -> [b]
map func [] = []
map func (x:xs) =
  (func x) : map func xs


is_even :: Int -> Bool -- must output Bool
is_even val = val `mod` 2 == 0

filter is_even [1..10] -- [2,4,6,8,10]

filter :: (a -> Bool) -> [a] -> [a]
filter predicate [] = []
filter predicate (x:xs) 
 | (predicate x) = x : (filter predicate xs)
 | otherwise = filter predicate xs
```
#### Reducers
- `foldl`: give function, accumulator start value, and list
	- Runs function on value in array + old value, returns new value left to right
```hs
adder acc x = acc + x
foldl adder 0 [7,-4,2] -- 5

adder2 x acc = acc + x
foldr adder2 0 [7,-4,2] -- 5



foldl f accum [] = accum
foldl f accum (x:xs) =
  foldl f new_accum xs
 where new_accum = (f accum x)
```
### lambda functions
- `\x -> x^3`
- `(\x y -> x^3+y^2) 10 3`
- `slopeIntercept m b = (\x -> m*x + b)`
	- Calling slopeIntercept returns lambda using specified m and b
	- m and b are "captured"
		- aka "Free variable": variable not an explicit parameter to lambda
- **Closure**: a function that
	- has zero or more arguments to run in the future
	- List of "free" variables and values when closure created, that were "captured"
	- Free variable: not explicit parameter to lambda (the ones that are captured)
### Partial Function Application:
- Call function with less than full number of arguments
	- Returns function with arguments of only missing parameters
	- Saves passed in values as a closure
- Examples:
```hs
cuber = map (\x -> x^3)
cuber [2,3,5] -- [8,27,125]
```
### Currying
- Instead of f(x, y, z) for 3 argument function, do (f(a) (b))(c)
```
function f(x) {
	function g(y) {
		function h(z) {
			return x + y + z
		}
		return h;
	}
	return g;
}

f(10)(20)(30)
```

```hs
mult3 x y z = x * y * z

mult3curry = \x = (\y = (\z = (x * y * z)))
```
- Haskell automatically curries every function (to allow partial function application!)
## Algebraic Data Types
```hs
data Color = Red | Green | Blue

-- Shape with variant types
data Shape = 
	Circle { radius :: Float, color :: Color } 
	Rectangle { width :: Float, height :: Float, color :: Color } |
	Triangle { base :: Float, height :: Float, color :: Color }
	Shapeless -- nullary variants
	
	
my_color = Red
my_circ = Circle { radius = 5.0, color = Blue }
```
- ADT & variant names must start with uppercase letter
- Compact version
```
data Color = Red | Green | Blue

data Shape = 
	Circle Float Color |
	Rectangle Float Float Color |
	Triangle Float Float Color |
	Shapeless
	deriving Show -- Add to allow printing out field values
	
my_circ = Circle 5.0 Blue
```
### Using ADTs
- Pattern Matching
```hs
getArea :: Shape -> Float
getArea (Circle 1 _) = pi
getArea (Circle r _) = pi * r^2
getArea (Rectangle w h _) = w * h
getArea (Triangle b h _) = .5 * b * h
getArea (Shapeless ) = 0
```
- Each version of function called "Branch"