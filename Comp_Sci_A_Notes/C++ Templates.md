```c++
template<typename TheType>
TheType TripleMin(TheType item1, TheType item2, TheType item3) {
   TheType minVal = item1; // Holds min item value, init to first item
   
   if (item2 < minVal) {
      minVal = item2;
   }
   if (item3 < minVal) {
      minVal = item3;
   }
   
   return minVal;
}

// Use with any variable type as long as all parameters with type `TheType` are the same and the type works with >/< operators
// You must put the template statement before all uses in a method header or at top of class
// Calling class functions or creating new objects with custom type:
   TripleItem<int> triInts(9999, 5555, 6666); // TripleItem class with ints