```c++

struct TimeHrMin {
   int hourValue;
   int minuteValue;
};

TimeHrMin ConvHrMin(int totalTime) {
   TimeHrMin timeStruct;

   timeStruct.hourValue  = totalTime / 60;
   timeStruct.minuteValue = totalTime % 60;

   return timeStruct;
}

int main() {
   int inTime;
   TimeHrMin travelTime;

   cout << "Enter total minutes: ";
   cin >> inTime;

   travelTime = ConvHrMin(inTime);

   cout << "Equals: ";
   cout << travelTime.hourValue << " hrs ";
   cout << travelTime.minuteValue << " mins";
 
   return 0;
}
```