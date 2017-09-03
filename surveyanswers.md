---
title: 330/370 Survey Answers
layout: single
author_profile: false
---
1) In Python, how do you create a variable "a" that is equal to 2?

The correct answer is a = 2, the other ones will throw errors in Python (though they might work in other languages)

2) How would you cast the string variable "a" that is equal to "2" into the integer 2?

The correct answer is int(a)

3) Which of the following is a valid dictionary in Python?

myExample = {'someItem': 2, 'otherItem': 20} (notice the curly brackets and colons)

4) What will you see if you execute: 
```python
for i in range(4,6): 
    print(i)
```

4,5 (this function generates the sequence starting at the first number and going up to, but not including, the last)

5) Read and tell us what gets output when you execute:

```python
def addItem(listParam):
    listParam += [1]

mylist = [1, 2, 3, 4]
addItem(mylist)
print(len(mylist))
```
The answer is 5. The function just adds a "1" to the end of the list every time you call it. so mylist gets bigger by 1 when we call addItem(myList) (it is then [1,2,3,4,1]) and the length is the number of elements (so 5)

Ok, now calculate your grade. Give yourself 2 points for each question where you knew the answer (wouldn't have had to look it up), give yourself 1 point for each question where you had narrowed it down to a couple of options (including the right one) but could have gotten it if you could just look up the syntax, if you didn't know the answer just count that as a 0 (but don't worry! we'll have a refresher/catch up the first few weeks). The max score is a 10. If you think that this quiz is a bad reflection of your python experience feel free to nudge the score up or down (but really, we just want to know this so we can partner you with someone appropriate).
