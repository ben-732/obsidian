---
Time Complexity: Linear
Space Complexity: Constant
---
https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm


An algorithm for finding the most common element in an array.  

## Algorithm
Have a variable for the current "answer" and for frequency;

Loop through numbers all numbers

if the frequency is 0, update the stored number and reset the frequency to 1. 

if the element in this position is equal to the current answer increment frequency. 
If not, decrement frequency. 

This is guaranteed to give an answer because *maths*