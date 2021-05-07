# Primitive Types

# TODO
1. Time complexities for each problem

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
```
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
```
We can use this to convert a character digit to an int, i.e.
```
'5'-'0' = 53-48 = 5
```

- Key methods in random and cmath

```
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
```


## Exercises

### [1. Computing the Parity of a Word](#1-computing-the-parity-of-a-word)
Parity of a binary word is 1 if the number of 1's in the word is odd,
and 0 otherwise.

- The expression
``` 
x&~(x-1) 
```
ISOLATES the lowest set bit in x.
```
x        = (00101100)_2 
x-1      = (00101011)_2 
~(x-1)   = (11010100)_2 
x&~(x-1) = (00000100)_2 
```
This bit may be removed from x by computing x XOR y.

- The expression
```
x&(x-1)
```
replaces the lowest set bit in x that is 1 with 0.
```
x       = (00101100)_2
x-1     = (00101011)_2
x&(x-1) = (00101000)_2
```

Four ways that we can compute the parity of a word:
1. Brute force, by iteratively testing values for each bit seen in a number
2. Grabbing the lowest 1-set bit, and keeping track of the amount we've seen.
3. Using a cache to store the parity of smaller-bit words, and grouping 
the number in non-overlapping ways.
4. Using associative and commutative properties of XOR to group bits and their
parities.

#### 1. Brute Force
```cpp
short Parity(unsigned long x)
{
  short result = 0;
  while(x)
  {
    result ^= (x&1);
    x >>= 1;
  }
  return result
}
```
Here we see we are using the XOR operation to track even/odd number of 
1-bits set seen.

#### 2. Tracking Lowest Set 1-Bit
```cpp
short Parity(unsigned long x)
{
  short result = 0;
  while(x)
  {
    result ^= 1; // flips result
    x &= (x-1)   // sets lowest set-bit to 0
  }
  return result;
} 
```
```
                       result = 0;
x        = (00101100); result = 1;
x        = (00101000); result = 0;
x        = (00100000); result = 1;
x        = (00000000); result = 0;
```

#### 3. Using a cache and grouping into non-overlapping ways
```cpp
short Parity(unsigned long x)
{
  const int kWordSize = 16;
  const int kBitMask = 0xFFFF;
  return precomputed_parity[x >> (3*kWordSize)] ^ 
         precomputed_parity[x >> (2*kWordSize) & kBitMask] ^
         precomputed_parity[x >> (kWordSize)] & kBitMask^
         precomputed_parity[x >> & kBitMask];
}
```
For this method, all we would need is a cache that stores the parity of all
16-bit words. We then correctly group the 64-bit number into 4 16-bit segments,
accomplished by shifting and extracting with a mask.

A lookup table for 2-bit words would be as follows:
```
<0,1,1,0> - The parities of <(00),(01),(10),(11)>
```
We can dynamically calculate the precomputed parity cache as we see 
16 bit numbers, or brute force generate (using method 1 or 2) the cache.

#### 4. Associativity and Commutativity of XOR
XOR operations are associative & commutative
```
parity of <b63,b62,...,b0> = parity of XOR of <b63,b62,...,b32> , <b31,b30,...,b0>
```
```cpp
short Parity(unsigned long x)
{
  x ^= x >> 32;
  x ^= x >> 16;
  x ^= x >> 8;
  x ^= x >> 4;
  x ^= x >> 2;
  x ^= x >> 1;
  return x & 0x1;
}
```
This process is illustrated with an 8-bit word
```
(11010111)
->
(1101) XOR (0111) = (1010)
(10) XOR (10) = (00)
(0) XOR (0) = 0;
0 & 0x1 = 0; 
<- Even parity
```

### 2. Swap Bits
```
x & (x-1)
```
clears the lowest set bit in x.
```
x & ~(x-1)
```
extracts the lowest set bit in x.

Problem statement: Implement code that takes as input a 64-bit integer
and swaps the bits at indices i and j.

```cpp
long SwapBits(long x, int i, int j)
{
  if( ((x >> i) & 1) != ((x >> j) & 1) )
  {
    unsigned long bit_mask = (1L << i) | (1L << j);
    x ^= bit_mask;
  }
  return x;
}
```
We only swap bits if they differ. 
Because we only attempt to flip if the bits differ, we can use the XOR operation
to flip the bits.
Time complexity: O(1)

### 3. Reverse Bits
Problem statement: Write a program that takes a 64-bit word and returns the 
64-bit word consisting of the bits of the input word in reverse order.
https://leetcode.com/problems/reverse-bits/
#### 1. Brute Force
We can iterate in reverse order through the 32 least significant bits, 
and swap each with the corresponding most significant bit.
The approach can be as follows:
```cpp
long ReverseBits(long x)
{
  for(int i=0, j=63; i<31, j>31; i++,j--)
  {
    if( (x >> j)&1 != (x >> i)&1)
    {
      // flip bits only if necessary
      unsigned long bit_mask = (1L << j) | (1L << i);
      x ^= bit_mask;
    }
  }
  return x;
}
```

#### 2. Using a cache
We can use a cache in a similar way we did with the parity problem.
If it s a 64-bit word, we can group the word into the following:
```
y3,y2,y1,y0
```
where y3 holds the most significant bits. We can store the reverse
order of 16 bits in an array, and form the reverse of x with
the reverse of y0 in the most significant bit positions, followed
by the reverse of y1, y2, then y3. 
```
rev = <(00),(10),(01),(11)>
input = (10010011)
y3 = 10
y2 = 01
y1 = 00
y0 = 11
reverse = <rev(y0),rev(y1),rev(y2),rev(y3)>
reverse = <rev(11),rev(00),rev(01),rev<10>
reverse = <rev[3],rev[0],rev[1],rev[2]>
reverse = <(11),(00),(10),(01)>
reverse = (11001001)
input =   (10010011)
```
```cpp
long ReverseBits(long x)
{
  const int kWordSize = 16;
  const int kBitMask  = 0xFFFF;
  return precomputed_reverse[x & kBitMask] << (3 * kWordSize) |
         precomputed_reverse[(x >> kWordSize) & kBitMask] << (2 * kWordSize) |
         precomputed_reverse[(x >> 2*kWordSize) & kBitMask] << (kWordSize) |
         precomputed_reverse[(x >> 3*kWordSize) & kBitMask];
```
The time complexity for this is O(n/L), for n-bit integers and L-bit cache keys

### 4. Find a Closest Integer With the Same weight
