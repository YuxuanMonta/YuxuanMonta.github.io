title: Homework 1 BIOSTAT823
date: 09-05-2021
author: Yuxuan Chen

# Problem 4: Largest palindrome product
Solved By: 489932

A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 × 99.
Find the largest palindrome made from the product of two 3-digit numbers.

This problem is easy to achieve with the help of python, I just follow the logic, finding the two 3-digit numbers and getting the product of that two numbers, and then checking if the product is palindrome or not. 
Next, I store all the results gotten from the last step and find the maximum.

```python

import numpy as np
import decimal

```

```python

def palindrome():
    '''Return the largest palindrome made from the product of two 3-digit numbers.'''
    res = []
    for i in range(100,1000):
        for j in range(100,1000):
            if str(i*j) == str(i*j)[::-1]:
                res.append(i*j)
    return np.max(res)

```

Run palindrome() and get 906609.

# Problem 55: Lychrel numbers

Solved By: 54082

If we take 47, reverse and add, 47 + 74 = 121, which is palindromic.

Not all numbers produce palindromes so quickly. For example,

349 + 943 = 1292,
1292 + 2921 = 4213
4213 + 3124 = 7337

That is, 349 took three iterations to arrive at a palindrome.

Although no one has proved it yet, it is thought that some numbers, like 196, never produce a palindrome. A number that never forms a palindrome through the reverse and add process is called a Lychrel number. Due to the theoretical nature of these numbers, and for the purpose of this problem, we shall assume that a number is Lychrel until proven otherwise. In addition you are given that for every number below ten-thousand, it will either (i) become a palindrome in less than fifty iterations, or, (ii) no one, with all the computing power that exists, has managed so far to map it to a palindrome. In fact, 10677 is the first number to be shown to require over fifty iterations before producing a palindrome: 4668731596684224866951378664 (53 iterations, 28-digits).

Surprisingly, there are palindromic numbers that are themselves Lychrel numbers; the first example is 4994.

How many Lychrel numbers are there below ten-thousand?

NOTE: Wording was modified slightly on 24 April 2007 to emphasise the theoretical nature of Lychrel numbers.

Depending on the definition of Lychrel number, I first generate a function **checklychrel** to check if the a number can form a palindrome in less than 50 iterations, which will check if a number of a lychrel number or not. Then I have **lychrel** function to add all occurrences up.

```python
def rev(n):
    '''Return the reverse of a number'''
    return int(str(n)[::-1])
```

```python
def checklychrel(x):
    '''Chek=ck whether a number is lychrel or not'''
    for i in range(50):
        x = rev(x) + x 
        if str(x)==str(x)[::-1]:
            return False
    return True
```

```python
def lychrel():
    '''Return the Lychrel numbers below ten-thousand.'''
    count = []
    for i in range(10000):
        count.append(checklychrel(i))
    return np.sum(count)

```
Run lychrel() and get 249.

# Problem 80:Square root digital expansion

Solved By : 19756

It is well known that if the square root of a natural number is not an integer, then it is irrational. The decimal expansion of such square roots is infinite without any repeating pattern at all.

The square root of two is 1.41421356237309504880..., and the digital sum of the first one hundred decimal digits is 475.

For the first one hundred natural numbers, find the total of the digital sums of the first one hundred decimal digits for all the irrational square roots.

In this problem, I first need to limit the range is the first 100 natural numbers, then check if they are irrational number or not by checking whether its square root is an integer. Then convert the data type from float to string and find the first 100 digits. 
Next, I convert the datatype back into integer and sum them up.
Note, I use *getcontext().prec = 200* here so that I can get the first 100 digits of an irrational number.


```python
def sqrt_dig_exp():
    """For the first 100 natural numbers, return the total of the digital sums of the first 100 decimal digits for all the irrational square roots."""
    getcontext().prec = 200
    res = 0
    for i in range(101):
        if not(np.sqrt(i).is_integer()):
            dec = str(Decimal(i).sqrt()).replace('.','')[:100]
            decimal = []
            for j in dec:
                decimal.append(int(j))
            singel = np.sum(decimal)
            res += singel
    return res
```

Run sqrt_dig_exp() and get 40886.





