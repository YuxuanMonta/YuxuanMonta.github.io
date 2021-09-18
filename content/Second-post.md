title: Homework 2 BIOSTAT823
date: 09-17-2021
author: Yuxuan Chen

# Homework 2 BIOSTAT823 Number theory and a Google recruitment puzzle

## Write a function to generate an arbitrary large expansion of a mathematical expression
This function is the start of this whole problem. This function can expand a expression and return its expansion. This function is pretty straightforward. The only problem is the usage of sympy. Here I use N() to expand the mathematical expression.

```python

from sympy import *
import numpy as np

```

```python

def expansion(exp,mul,digits):
    '''Generate an arbitrary large expansion of a mathematical expression'''
    if mul%1 != 0 or digits%1 != 0:
        raise TypeError("Inputed multiplier and number of digits should be integers!")
    return N(mul*exp, digits)

```

Run palindrome() and get 906609.

## Write a function to check if a number is prime

Here is a function to check if a number is a prime or not. I first tried Sieve of Eratosthenes, but that algorithm is not that efficient. Thus, I simply check prime by divide the number with integer that is smaller than the square root of itself.

```python
def is_prime(n:int) -> int:
    '''check whether integer n is prime or not'''
    
    # 1 is not prime number and n should be positive
    if n == 1 or n <= 0:
        return False
    
    # check integer from 2 to int(sqrt(x))
    i = 2
    while i*i <= n:
        if n % i == 0:
            return False
        i += 1
    return True
```


## Write a function to generate sliding windows of a specified width from a long iterable

This function returns sliding windows of a specified width of a long number. Note those last some outputs in the list is not long enough. However, this is not a problem because we deal with that in the main function.


```python
def slid_win(n, win):
    """Generate sliding windows of a specified width"""
    if win%1 != 0 :
        raise TypeError("Inputed window length and number of digits should be integers!")
        
    res = [] 
    dec_str = str(n).split('.')[1]
    for i in range(len(dec_str)):
        res.append(dec_str[i:i+win])    
    return res 
```

## Unit test for three functions

Here I do unit test to check my functions.
```python
%%file helpers.py

from sympy import *
import numpy as np

def expansion(exp,mul,digits):
    '''Generate an arbitrary large expansion of a mathematical expression'''
    if digits%1 != 0:
        raise TypeError("Inputed multiplier and number of digits should be integers!")
    return N(mul*exp, digits)


def is_prime(n:int) -> int:
    '''check whether integer n is prime or not'''
    
    # 1 is not prime number and n should be positive
    if n == 1 or n <= 0:
        return False
    
    # check integer from 2 to int(sqrt(x))
    i = 2
    while i*i <= n:
        if n % i == 0:
            return False
        i += 1
    return True

def slid_win(n, win):
    """Generate sliding windows of a specified width!"""
    if win%1 != 0 :
        raise TypeError("Inputed window length and number of digits should be integers!")
        
    res = [] 
    dec_str = str(n).split('.')[1]
    for i in range(len(dec_str)):
        res.append(dec_str[i:i+win])    
    return res
```




```python
%%file test.py

import unittest
from helpers import expansion, is_prime, slid_win
import math

class TestHelpers(unittest.TestCase):
    def test_expand(self):
        self.assertEqual(str(expansion(5, math.pi, 6)), str(format(5*math.pi, '.4f')))
        self.assertEqual(str(expansion(1, math.e,100)), str(format(math.e, '.99f')))
        self.assertEqual(str(expansion(10, math.pi, 22)), str(format(10*math.pi, '.20f')))
    
    def test_prime(self):
        self.assertFalse(is_prime(4))
        self.assertTrue(is_prime(47))
        self.assertTrue(is_prime(37))
        self.assertTrue(is_prime(7))
        
    def test_slid_win(self):
        self.assertEqual(slid_win(3.1415926, 4), ['1415', '4159', '1592', '5926','926','26','6'])
    
if __name__ == '__main__':
    unittest.main()

```


```python
! python3 -m unittest test.py
```

Finished check and functions are good!


## Main Function
Here is the main function. I also deal the those number start with 0 here.

```python
def find_prim(mul, exp, win , digits = 1000):
    '''Return the first prime with certain number of digits in a expression.'''
    num = expansion(exp,mul,digits)
    num_slide = slid_win(num, win)
    
    for i in range(len(num_slide)):
        if len(num_slide[i]) < win:
            return("no prime")
        if num_slide[i][0] != 0:
            if is_prime(int(num_slide[i])):
                return num_slide[i]
```

```python
find_prim(17, pi, 10)
```
Get the first 10-digit prime in the decimal expansion of 17π, 8649375157.















