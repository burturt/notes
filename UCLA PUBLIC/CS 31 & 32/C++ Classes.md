```c++
#include <iostream>
#include <string>
#include <vector>
using namespace std;

class Tutor {
   public:
      Tutor(string = "NoName", int = 0);
      void Print() const;

   private:
      string name;
      vector<string> students;
};

Tutor::Tutor(string tutorName, int numStudents) : name(tutorName), students(numStudents) {}
// Asigns tutorName to name, numStudents to students

void Tutor::Print() const {
   if (students.size() == 0){
      cout << name << " is solo" << endl;
   }
   else {
      cout << name << " tutors " << students.size() << " students" << endl;
   }
}

int main() {
   Tutor myTutor("Isa", 0);
   Tutor yourTutor("Jay", 6);

   myTutor.Print();
   yourTutor.Print();

   return 0;
}
```

Separating into files:
- `class.h`: header file with class defition
- `class.cpp`: c++ file with class code. Must use `#include "class.h"`
- `whatever.cpp`: main file that calls class. Must use `#include class.h`

`this->var` - java `this` same but uses `->` instead of `.`

Overloading operators:
```c++
#include <iostream>
using namespace std;

class TimeHrMn {
public:
   TimeHrMn(int timeHours = 0, int timeMinutes = 0);
   void Print() const;
   TimeHrMn operator+(TimeHrMn rhs) ;
private:
   int hours;
   int minutes;
};

// Overload + operator for TimeHrMn
TimeHrMn TimeHrMn::operator+(TimeHrMn rhs) {
   TimeHrMn timeTotal;
   
   timeTotal.hours   = hours   + rhs.hours;
   timeTotal.minutes = minutes + rhs.minutes;
   
   return timeTotal;
}

TimeHrMn::TimeHrMn(int timeHours, int timeMinutes) {
   hours  = timeHours;
   minutes = timeMinutes;
}

void TimeHrMn::Print() const {
   cout << "H:" << hours << ", " << "M:" << minutes << endl;
}


```