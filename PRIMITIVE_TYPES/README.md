# Primitive Types

We can combine shifting and masking to count the number of bits in a number.
Most implementations of C++ use 32 or 64 bits for int, Java always uses 32.
```cpp
short CountBits(unsigned int x)
{
  while(x)
  {
    num_bits += x & 1;
    x >>= 1;
  }
  return num_bits;
}
```
The time complexity of this function is O(n).

Commutativity and associativity can be used to perform operations
in parallel and reorder operations.

- Constants in C++
```
numeric_limits<int>::min()
numeric_limits<float>::max()
numeric_limits<double>::infinity()
```
* Take extra care when comparing float values.

Know how to convert integers, characters, strings...
- ASCII:
    48 0
    49 1
    50 2
    51 3
    52 4
    53 5
    54 6
    55 7
    56 8
    57 9
We can use this to convert a character digit to an int, i.e.
    '5'-'0' = 53-48 = 5

- Key methods in random and cmath
cmath
    abs(-199)
    fabs(-2.44)
    ceil(4.55)
    floor(5.33)
    min(x,34)
    max(43,y)
    pow(3,5)
    log(53.1)
    sqrt(64)
random
    uniform_int_distribution<>dis(1,10) -> integer [1,10]
    uniform_real_distruction<double>dis(1.6,5.4) -> float [1.6,5.4]
    generate_canonical<double,10> -> float [0,1]


## Exercises

### Computing the Parity of a Word
Parity of a binary word is 1 if the number of 1's in the word is odd,
and 0 otherwise.

- The expression
``` 
x&~(x-1) 
```
clears the lowest set bit in x.
    ---------------------------------
    | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 1 |
    ---------------------------------
