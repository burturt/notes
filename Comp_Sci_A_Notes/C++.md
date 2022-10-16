```c++
#include <iostream> // allows cin cout
#include <string>
#include <math>
#include <cstdlib>
#include <iomanip> // for setprecision
#include <ctime> // allow time()
#include <cctype> //allow character manipulation, isalpha, isdigit, isspace, toupper, tolower
#include <cassert> // allow assert(boolean) unit testing
#include "header.h" // Add the header.h header to the program 
using namespace std;

int main() {
	const int MAX_SIZE = 100;
	int test = 1; // 32
	char test2 = 'a';
	string meow = "meow";
	long long test3; // short: 16. long: 32. long long 64
	int& test = test; // Declare reference variable. Everything is pass-by-value unless using & sign
	
	cout << fixed << setprecision(2); // Set output precision to 2 decimal places
	
	cin >> test; // Gets next chunk of non-whitespace input
	cout << test << " " << test2; // 1 a
	
	getline(cin, string);
	
	// Seed random:
	srand(time(0));
	rand(); //get integer between 0 and RAND_MAX, machine dependent but at least 32767+
	
	meow.at(3); //get character at index 3, can also be assigned:
	meow.at(3) = 'u';
	meow.size(); //length, can also use string.length()
	meow.append("strings only"); //append **string** to end. Use push_back for char
	meow.find('m'); //find index of character, returns string::npos if not found
	meow.find('m', 2); //starts at index 2
	meow.substr(index, length); //substring starting at index and with length
	
	
	
	return 0;
}
```


header.h:
```c++
#ifndef MYCUSTOMHEADER_H
#define MYCUSTOMHEADER_H
// Above 2 prevent header from loading multiple times
int ThreeIntsSum(int num1, int num2, int num3);
int ThreeIntsAvg(int num1, int num2, int num3);
```


header.cpp:
```c++
int ThreeIntsSum(int num1, int num2, int num3) {
   return (num1 + num2 + num3);
}

int ThreeIntsAvg(int num1, int num2, int num3) {
   int sum;
   sum = num1 + num2 + num3;
   return (sum / 3);
}
```
