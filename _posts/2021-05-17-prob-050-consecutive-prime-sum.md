---
layout: post
title:  "Consecutive Prime Sum"
date:   2021-05-17 11:13:00 -0700
category: ["learning"]
---

# Consecutive Prime Sum

## Context

I cut my teeth on Java by solving problems on [Project Euler](https://projecteuler.net). At one point, the solution to Problem 50 eluded me for a long time: 7 years to be precise. On a random afternoon last week, I was browsing (and cringing at) some of my old code when I came across this problem once again.

I decided to give it one more try...

## Problem

From the [problem page](https://projecteuler.net/problem=50):

> The prime 41, can be written as the sum of six consecutive primes:
`41 = 2 + 3 + 5 + 7 + 11 + 13`
>
> This is the longest sum of consecutive primes that adds to a prime below one-hundred.
>
> The longest sum of consecutive primes below one-thousand that adds to a prime, contains 21 terms, and is equal to `953`.
>
> Which prime, below one-million, can be written as the sum of the most consecutive primes?

## Solution

The first thought to this problem is "for each prime under a million, find a representation in terms of a sum of consecutive primes".

A simpler solution, most likely, is to invert this. Let's find the sums of consecutive primes, and keep track of the longest such expression that sums to a prime.


```python
import bisect
import dataclasses
import itertools
import timeit
import math
from typing import Generator, Callable, Iterator
```

### Solution Type

Let's first define what an "answer" will look like. What we want is:

1. The prime itself
2. The consecutive primes that add up to it
3. The length of the chain (number of primes from 2)


```python
@dataclasses.dataclass
class ConsecutivePrimeSum:
    prime: int
    consecutive_primes: list

    @property
    def chain_length(self):
        return len(self.consecutive_primes)
```

### Get All Primes Below 1 Million

My favorite mechanism of generating primes is [Eratosthenes' Sieve](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)


```python
def eratosthenes_sieve(n: int) -> Callable[[int], bool]:
    """Returns a function that is an efficient implementation of is_prime

        Generates a Eratosthenes' sieve for primes, and uses it as a cache for a wrapped function
    """
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False

    for i in range(2, int(math.sqrt(n)) + 1):
        # if i is marked as "not prime", its multiple must have been too
        # so nothing to do here
        if not is_prime[i]:
            continue

        # If i is not marked as "not prime", it MUST be prime
        # Mark all multiples of i as not prime
        multiples_of_i = range(i**2, n + 1, i)
        for j in multiples_of_i:
            is_prime[j] = False

    return lambda x: is_prime[x]
```


```python
def primes_under(n: int) -> Iterator[int]:
    is_prime = eratosthenes_sieve(n)
    return [i for i in range(n) if is_prime(i)]
```

Let's do a quick check:


```python
primes_under_100 = primes_under(100)
print(primes_under_100)
```

    [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97]


### Prefix Sums

What we now want is sums over "windows" of varying sizes over this array:

<table>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>5</td>
        <td>7</td>
        <td>11</td>
    </tr>
    <tr>
        <td style="background-color:yellow">2</td>
        <td>3</td>
        <td>5</td>
        <td>7</td>
        <td>11</td>
    </tr>
    <tr>
        <td style="background-color:yellow">2</td>
        <td style="background-color:yellow">3</td>
        <td>5</td>
        <td>7</td>
        <td>11</td>
    </tr>
    <tr>
        <td style="background-color:yellow">2</td>
        <td style="background-color:yellow">3</td>
        <td style="background-color:yellow">5</td>
        <td>7</td>
        <td>11</td>
    </tr>
    <tr>
        <td style="background-color:yellow">2</td>
        <td style="background-color:yellow">3</td>
        <td style="background-color:yellow">5</td>
        <td style="background-color:yellow">7</td>
        <td>11</td>
    </tr>
    <tr>
        <td style="background-color:yellow">2</td>
        <td style="background-color:yellow">3</td>
        <td style="background-color:yellow">5</td>
        <td style="background-color:yellow">7</td>
        <td style="background-color:yellow">11</td>
    </tr>
    <tr>
        <td>2</td>
        <td style="background-color:cyan">3</td>
        <td>5</td>
        <td>7</td>
        <td>11</td>
    </tr>
    <tr>
        <td>2</td>
        <td style="background-color:cyan">3</td>
        <td style="background-color:cyan">5</td>
        <td>7</td>
        <td>11</td>
    </tr>
    <tr>
        <td>2</td>
        <td style="background-color:cyan">3</td>
        <td style="background-color:cyan">5</td>
        <td style="background-color:cyan">7</td>
        <td>11</td>
    </tr>
    <tr>
        <td>2</td>
        <td style="background-color:cyan">3</td>
        <td style="background-color:cyan">5</td>
        <td style="background-color:cyan">7</td>
        <td style="background-color:cyan">11</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td style="background-color:pink">5</td>
        <td>7</td>
        <td>11</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td style="background-color:pink">5</td>
        <td style="background-color:pink">7</td>
        <td>11</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td style="background-color:pink">5</td>
        <td style="background-color:pink">7</td>
        <td style="background-color:pink">11</td>
    </tr>
</table>

The naive way would be to loop over every i, every j, and find sums, an $O(n^3)$ operation. A slightly better approach is to find prefix sums, which will bring this down to $O(n^2)$


```python
prefix_sums = [0] + list(itertools.accumulate(primes_under_100))
print(list(prefix_sums))
```

    [0, 2, 5, 10, 17, 28, 41, 58, 77, 100, 129, 160, 197, 238, 281, 328, 381, 440, 501, 568, 639, 712, 791, 874, 963, 1060]


Getting the sum of all primes between and including (say) 3 and 7 is simply


```python
end = primes_under_100.index(7)
start = primes_under_100.index(3)
prefix_sums[end + 1] - prefix_sums[start]
```




    15



The next step is, for each pair of indices, find the sum of all primes between those two indices, and if it is prime, update our answer


```python
def consecutive_prime_sums(n, debug=False) -> Generator[ConsecutivePrimeSum, None, None]:
    is_prime = eratosthenes_sieve(n)
    primes_under_n = primes_under(n)

    # prepend a 0 for nicer indexing
    prefix_sums = [0] + list(itertools.accumulate(primes_under_n))

    num_primes = len(primes_under_n)

    max_length_until_now = 0

    # Consider all chains starting from the ith prime
    for i in range(num_primes):
        # We iterate backwards for the end, since we are looking for the longest chain
        # Once we find a chain from [i,j], there is no point in looking for smaller j
        j_start = num_primes - 1

        # chains with a length smaller than the longest one we have found so far
        # will not contribute to the answer, so we can and should skip them
        j_end = max_length_until_now + i - 1

        for j in range(j_start, j_end, -1):
            chain_length = j - i + 1

            # prime_sum includes both the ith and jth prime
            consecutive_prime_sum = prefix_sums[j + 1] - prefix_sums[i]

            # if this sum is greater than the highest acceptable prime for this problem
            # skip it
            if consecutive_prime_sum > n:
                continue

            if is_prime(consecutive_prime_sum):
                max_length_until_now = max(max_length_until_now, chain_length)
                yield ConsecutivePrimeSum(consecutive_prime_sum, primes_under_n[i:j + 1])

                # Smaller j's with the same i will never give a better answer
                break
```


```python
def solve(n, solver):
    return max(solver(n), key=lambda x: x.chain_length)
```

Let's test out the basic cases:


```python
solve(100, solver=consecutive_prime_sums)
```




    ConsecutivePrimeSum(prime=41, consecutive_primes=[2, 3, 5, 7, 11, 13])




```python
solve(1000, solver=consecutive_prime_sums)
```




    ConsecutivePrimeSum(prime=953, consecutive_primes=[7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89])



We have a working solution! :)

### Performance

Before we continue with finding the solution for `n = 1000000`, we need to confirm if this is an acceptable solution, i.e. will it end within a minute?


```python
from tabulate import tabulate

def test_performance(solver):
    # Simple geometric progression to see how the algorithm scales
    ns = [100 * 2**(n - 1) for n in range(1, 14)]

    data = []
    for n in ns:
        start = timeit.default_timer()
        ans = solve(n, solver)
        end = timeit.default_timer()
        data.append([n, round(end-start, 6), ans.prime])
    return data
```


```python
data = test_performance(consecutive_prime_sums)
```


```python
print(tabulate(data, headers=["n", "time (s)", "answer"], tablefmt="fancy_grid"))
```

    ╒════════╤════════════╤══════════╕
    │      n │   time (s) │   answer │
    ╞════════╪════════════╪══════════╡
    │    100 │   8.5e-05  │       41 │
    ├────────┼────────────┼──────────┤
    │    200 │   0.000209 │      197 │
    ├────────┼────────────┼──────────┤
    │    400 │   0.000445 │      379 │
    ├────────┼────────────┼──────────┤
    │    800 │   0.001347 │      499 │
    ├────────┼────────────┼──────────┤
    │   1600 │   0.005278 │     1583 │
    ├────────┼────────────┼──────────┤
    │   3200 │   0.017518 │     2909 │
    ├────────┼────────────┼──────────┤
    │   6400 │   0.068539 │     6079 │
    ├────────┼────────────┼──────────┤
    │  12800 │   0.198685 │    12713 │
    ├────────┼────────────┼──────────┤
    │  25600 │   0.682807 │    25237 │
    ├────────┼────────────┼──────────┤
    │  51200 │   2.41372  │    49279 │
    ├────────┼────────────┼──────────┤
    │ 102400 │   8.6131   │   102001 │
    ├────────┼────────────┼──────────┤
    │ 204800 │  30.0977   │   203279 │
    ├────────┼────────────┼──────────┤
    │ 409600 │ 106.413    │   408479 │
    ╘════════╧════════════╧══════════╛


Hmm, it looks like this is definitely not fast enough. Since ours is an $O(n^2)$ algorithm, solving for 1 million will take roughly 480s, close to 8 minutes.

### Optimization

Let's take a quick look at the primes under a million:


```python
primes_under_million = primes_under(1000_000)
```


```python
len(primes_under_million)
```




    78498




```python
primes_under_million[-10:]
```




    [999863,
     999883,
     999907,
     999917,
     999931,
     999953,
     999959,
     999961,
     999979,
     999983]



Can we get an upper bound for the answer? We can! Let us find a `k` such that the sum of the first `k` primes (i.e. the smallest `k` primes) exceeds a million:


```python
k = 0
running_sum = 0
while running_sum < 1000_000:
    running_sum += primes_under_million[k]
    k += 1
```


```python
k
```




    547



Now we can limit our search to chains of maximum length k = `547`.

In the general case, if we can find a `k` such that the sum of the smallest `k` primes exceeds `n`, then we have an upper bound that can significantly speed up the solution.

We can incorporate this into our solver (eliding all comments for succintness):


```python
def faster_consecutive_prime_sums(n, debug=False) -> Generator[ConsecutivePrimeSum, None, None]:
    is_prime = eratosthenes_sieve(n)
    primes_under_n = primes_under(n)

    prefix_sums = [0] + list(itertools.accumulate(primes_under_n))

    # Since prefix_sums is sorted by nature, use binary search to find n
    max_possible_chain_length = bisect.bisect_left(prefix_sums, n) + 1
    if debug:
        print(f"Max possible chain length for {n} is {max_possible_chain_length}")

    num_primes = len(primes_under_n)

    max_length_until_now = 0

    # Consider all chains starting from the ith prime
    for i in range(num_primes):
        j_start = min(i + max_possible_chain_length, num_primes - 1)
        j_end = max(max_length_until_now + i - 1, 0)

        for j in range(j_start, j_end, -1):
            chain_length = j - i + 1

            consecutive_prime_sum = prefix_sums[j + 1] - prefix_sums[i]

            if consecutive_prime_sum > n:
                continue

            if is_prime(consecutive_prime_sum):
                max_length_until_now = max(max_length_until_now, chain_length)
                yield ConsecutivePrimeSum(consecutive_prime_sum, primes_under_n[i:j + 1])
                break
```


```python
data = test_performance(faster_consecutive_prime_sums)
print(tabulate(data, headers=["n", "time (s)", "answer"], tablefmt="fancy_grid"))
```

    ╒════════╤════════════╤══════════╕
    │      n │   time (s) │   answer │
    ╞════════╪════════════╪══════════╡
    │    100 │   0.000144 │       41 │
    ├────────┼────────────┼──────────┤
    │    200 │   0.000188 │      197 │
    ├────────┼────────────┼──────────┤
    │    400 │   0.000614 │      379 │
    ├────────┼────────────┼──────────┤
    │    800 │   0.00085  │      499 │
    ├────────┼────────────┼──────────┤
    │   1600 │   0.000797 │     1583 │
    ├────────┼────────────┼──────────┤
    │   3200 │   0.001656 │     2909 │
    ├────────┼────────────┼──────────┤
    │   6400 │   0.004762 │     6079 │
    ├────────┼────────────┼──────────┤
    │  12800 │   0.006628 │    12713 │
    ├────────┼────────────┼──────────┤
    │  25600 │   0.011502 │    25237 │
    ├────────┼────────────┼──────────┤
    │  51200 │   0.025432 │    49279 │
    ├────────┼────────────┼──────────┤
    │ 102400 │   0.043048 │   102001 │
    ├────────┼────────────┼──────────┤
    │ 204800 │   0.104071 │   203279 │
    ├────────┼────────────┼──────────┤
    │ 409600 │   0.209027 │   408479 │
    ╘════════╧════════════╧══════════╛


This appears to satisfy our performance problems: computing for a million should take roughly half a second:


```python
%%timeit -n3 -r3
solve(1_000_000, solver=faster_consecutive_prime_sums)
```

    491 ms ± 14.4 ms per loop (mean ± std. dev. of 3 runs, 3 loops each)

