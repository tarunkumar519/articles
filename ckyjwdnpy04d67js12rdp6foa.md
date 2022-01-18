## C++  Palindrome (Beginner)

## Road to C++ series

In this series, i will explain and write code for different types of C++ problems from beginner to expert level. This series is useful for anyone trying to master C++ from scratch and also just starting C++ and preparing to start their software journey.

(Also this is my first series of posts in my blogging journey. If you want to correct any mistakes or give any advice i'm open to conversation in the comments! )

Let's beign !

- - -

A palindrome is a number or word or phrase which reads the same from forward as well as backward. We will only use numbers in this tutorial.

For example, **121** or **969**.

Now one of the basic problems of C++ is finding if the given number is palindrome or not. We can definitely see a number and say that, but making a program find it out is interesting. We will do exactly that.

## What we will do

We will write a C++ program to take input of a number and the program outputs "palindrome" if the number is palindrome or "not a palindrome" if it's not. You don't even have to know any C++ before this, its simple! yet it's most asked question in interviews of several  [service based companies](https://www.interviewbit.com/blog/service-based-companies-in-india/) .

## How we will do (The Basics)

First we initialize a variable number using (int number). Here int means integer which is telling the program that out number is an integer. we use 'cin' followed by these brackets '>>' to take input in C++ and to print output we use 'cout' followed by these brackets '<<'

We cin( pronounced as *see in*) a number as input, do math on it and produce output using cout( pronounced as *see out*)

We take the input number into a variable, we reverse the number store it in another variable and check if the both are equal at the end. If the original number and it's reverse are equal then it's a palindrome.

So, how do we reverse a number? The beauty lies in logic.

Assume our number is 121 and work on..

```
#include <iostream>
using namespace std;
int main () {
// Initialize variable
  int number ;
  cin >> number;
  int remainder = 0, reverse = 0;
  // To reverse a number store it in temp
  int temp = number;
  // while loop
  while (temp != 0) {
    // Get the last digit of temp
    remainder = temp % 10;
    // Store the remainder after the initially stored value in reverse
    reverse = reverse * 10 + remainder;
    // Remove the last digit of temp
    temp = temp / 10;
  }
  // if condition
  if (number == reverse) {
    cout << "palindrome";
  } else {
    cout << "not a palindrome";
  }
return 0;
}
``` 
visit  [here](https://replit.com/@tarunkumar519/C-palindrome) to run the program, enter a number and check the output. 

## How we will do (The logic explained)

To reverse a given number, we store our number in temp( just for now, ok?) and then iterate through the while loop while the temp is not equal to 0. 

whenever you apply modulus operation (%) on two numbers you get the remainder.(that means you get the last digit !)

For example: 121%10 = 1 (the last digit !)

In the body of the while loop, we get the last digit of temp(using the famous modulus operator %) and store it in the remainder.

 We add the remainder in reverse after the initially stored value in reverse. For example in the first while loop iteration,


``` 
reverse = reverse * 10 + remainder
``` 
which means : reverse = 0*10 + 1 which equals 1.

See we got the first digit of the reverse number.
Then, we divide the temp by 10 to remove the very last digit. (since we already got the last digit)

The while loop repeats this process until temp = 0, once temp = 0 it program compiler exits the loop.

So we get next two digits ( 2 and 1 respectively) in the same way. So our final reverse number value equals 121. The if-else loop checks if number = reverse.


```
  if (number == reverse) {
    cout << "palindrome";
  } else {
    cout << "not a palindrome";
  }
``` 
That's it! The same logic can be used to reverse any given number and print the reverse of the number which is another basic problem. Any suggestions or issues? drop a comment. Subscribe to my newsletter so we will all be here on the next post!