---
title: "Advent of Code Day 1 in Go"
layout: post
date: 2020-12-16 11:00 PM
tag:
- blog
hidden: false # don't count this post in blog pagination
description: "A deep dive into solving AoC day 1 with Go"
category: blog
author: randykinne
externalLink: false
---

# About Advent of Code

(copied from [here](https://adventofcode.com/2020/about)) 
> Advent of Code is an Advent calendar of small programming puzzles for a variety of skill sets and skill levels that can be solved in any programming language you like. People use them as a speed contest, interview prep, company training, university coursework, practice problems, or to challenge each other.

## Problem ##

The link to the full problem [is here.](https://adventofcode.com/2020/day/1)

To summarize the day 1 problem: we need to find the two items in a list of integers that sum to `X` and then multiply those two items to recieve the correct answer. 

In this case, `X` is `2020`, so we need to find the items that add up to `2020` and multiply those items to get the answer.
 
As `2020` is even, we have two potential scenarios:
1. Two numbers repeat and are exactly half of `2020`, or `1010`
2. One number is greater than the other number, meaning one is greater than `1010` and one is less than `1010`.

After quickly looking at our input, there are no repeating numbers, so one of our values must include one greater than `1010` and one less than `1010`.

## Solution

If we split our input into two groups, one greater than half of `2020` and one less than half of `2020`, we can create two groups which each contain one of the answers and then compare each number

We can break down our total solution into the following steps:
1. Parse the input
2. Split the input into two groups, one higher than half of `2020` and one lower than half
3. Find the value from each which sum to `2020`

Depending on how we find the value from group, we may need to add a few steps to make it easier to find the value.

## Parsing input


**Brute-force Method (Linear Search)**

We could use a nested loop to loop over both of our lists and compare every item sum to 2020 until we find the correct 2 that sum to `2020`. 

A basic implementation of this in Go would be:

{% highlight go %}
func FindPair(x int, a []int) (int, int) {
    for _, i := range greater_than_list {
        for _, j := range less_than_list {
            if i + j == 2020 {
                return i, j
            }
        }
    }
}
{% endhighlight %} 

<img class="image" src="https://carbon.now.sh/?bg=rgba%28171%2C+184%2C+195%2C+1%29&t=monokai&wt=none&l=text%2Fx-go&ds=true&dsyoff=20px&dsblur=68px&wc=true&wa=true&pv=56px&ph=56px&ln=false&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=%252F%252F%2520FindPair%2520of%2520integers%2520that%2520sum%2520to%25202020%250Afunc%2520FindPair%28x%2520int%252C%2520a%2520%255B%255Dint%29%2520%28int%252C%2520int%29%2520%257B%250A%2520%2520%2520%2520for%2520_%252C%2520i%2520%253A%253D%2520range%2520greater_than_list%2520%257B%250A%2520%2520%2520%2520%2520%2520%2520%2520for%2520_%252C%2520j%2520%253A%253D%2520range%2520less_than_list%2520%257B%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520if%2520i%2520%252B%2520j%2520%253D%253D%25202020%2520%257B%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520return%2520i%252C%2520j%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257D%250A%2520%2520%2520%2520%2520%2520%2520%2520%257D%250A%2520%2520%2520%2520%257D%250A%257D" alt="O(N^2)">

This solution would work on small inputs, but would quickly take too long to compute as the compute-time would grow exponentially at a rate of [O(n^2)](https://developerinsider.co/big-o-notation-explained-with-examples/) compared to a linearly-growing input.

<img class="image" src="https://lukasmestan.com/assets/images/o-n2.png" alt="O(N^2)">
<figcaption class="caption">Just look at this graph.</figcaption>

In other words, this is not a scalable solution and would quickly take too long to compute if the size of our input was large.

**Binary Search**

Generally the best-accepted alternative to linear search is using binary search.

To summarize, rather than search each object one by one to find a match, we take a sorted array and compare the item we're looking for with the object in the middle of the array. Depending on whether our object is greater than or less than the middle, we throw away the known incorrect half of the array and repeat the search using the correct half of the array.

Luckily for us, Go's `sort` package includes a binary search implementation that we can use to find our two values:

{% highlight go %} 
// Find index of x in sorted array using binary search
func Find(x int, arr []int) int {
	i := sort.Search(len(arr), func(i int) bool { return arr[i] >= x })
	if i < len(arr) && arr[i] == x {
		return i
	}
	return 0
}

// FindPair of i1 value that equals an i2 value, returns the indices
func FindPair(i1 []int, i2 []int) (int1 int, int2 int) {
	for i, item1 := range i2 {
		j := Find(item1, i1)
		if i1[j] == item1 {
			return j, i
		}
	}
	return 0, 0
}
{% endhighlight %} 

## Benchmarks

A quick benchmark with our input comparing our naive approach above to Go package `sort`'s `Search()` binary search implementation, we can see that:

```
Linear: 443ns
Binary: 1.299µs
```

The benchmark shows unexpected results in favor of Linear search. After closer examination, it's likely that my benchmark was flawed in a few ways:
1. The time it takes to sort was not factored into Binary's result, as the arrays for both had already been sorted
2. Multiple benchmarks were not done (ie: the input order didn't change), so it's possible that the binary search was worst case vs linear best case considering that both benchmarks were trying to find a single pair of matching values in two arrays


