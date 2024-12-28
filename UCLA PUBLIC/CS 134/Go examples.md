```go
package main

/** UCLA CS 134, FALL 2024

Credit:
Professor Wyatt Lloyd, Professor Michael Freedman, Jennifer Lam -- Princeton University

**/

import "fmt"

// EXERCISE 1. Print the first 10 squared numbers.
func ex1() {

	for i := 1; i <= 10; i++ {
		fmt.Printf("%d ", i*i)
	}
	fmt.Println()
}

// EXERCISE 2. Print the first 10 fibonacci numbers. 
func ex2() {

	a := 1
	b := 1

	fmt.Printf("%d %d ", a, b)

	for i := 2; i < 10; i++ {
		fmt.Printf("%d ", a+b)
		temp := b
		b = a + b
		a = temp
	}
	fmt.Println()
}

// EXERCISE 3. Fizz-buzz: replace multiples of 3 with Fizz and multiples of 5 
// with Buzz, and multiples of both with FizzBuzz. Print the first 10 numbers in
// this sequence. 
func ex3() {

	for i := 1; i <= 10; i++ {
		if i%3 == 0 && i%5 == 0 {
			fmt.Printf("fizzbuzz ")
		} else if i%3 == 0 {
			fmt.Printf("fizz ")
		} else if i%5 == 0 {
			fmt.Printf("buzz ")
		} else {
			fmt.Printf("%d ", i)
		}
	}
	fmt.Println()
}

// EXERCISE 4. Write a function that reverses a slice. 
func reverse(s []int) {

	for i := 0; i < len(s)/2; i++ {
		temp := s[i]
		s[i] = s[len(s)-1-i]
		s[len(s)-1-i] = temp
	}
}

func ex4() {
	var a [10]int // makes an array of 10 ints
	s := []int{1, 2, 3, 4, 5} // Makes a slice from new array
	s2 := a[0:4] // includes 0 1 2 3, changes to this changes a
	fmt.Printf("unreversed %+v\n", s, len(s))

	reverse(s)
	fmt.Printf("reversed: %+v\n", s)
}

// EXERCISE 5. Write a function that returns the number of unique items in a slice.
func distinct(s []int) int {
	count := 0
	distMap := make(map[int]int)
	for _, v := range s {
		if _, ok := distMap[v]; !ok {
			count = count + 1
			distMap[v] = 0 // placeholder value 0
		}
	}
	return count
}

func ex5() {

	s := []int{1, 2, 2, 3, 3, 3, 4, 4, 4, 4}
	fmt.Printf("distinct: %d\n", distinct(s))
}
```
## Dynamic slices
```go

a := make([]int, 5) // makes dynamic sized slice with 5 zeroed array

s := append(a, 0)

for idx, val := range s {
	fmt.Printf("%d: %d\n", idx, val)
}

m := make(map[string]string)
m["test"] = "val"
delete(m, "test")

elem, ok := m[key] // check if key exists
```