---
title: "2020 Advent of Code Day 1 in Go"
layout: post
date: 2020-12-16 11:00 PM
tag:
- blog
hidden: false # don't count this post in blog pagination
description: "A deep dive into solving 2020 AoC day 1 with Go"
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

If we split our input into two groups, one greater than half of `2020` and one less than half of `2020`, we can create two groups which each contain one of the answers and then compare each number.

We can break down our total solution into the following steps:
1. Parse the input
2. Split the input into two groups, one higher than half of `2020` and one lower than half
3. Find the value from each which sum to `2020`

Depending on how we find the value from group, we may need to add a few steps to make it easier to find the value.

## Parsing input

Fortunately, Go's `ioutil` package has a nice built-in way to read our input file and convert it to an array of bytes.

{% highlight go %}
data, err := ioutil.ReadFile("input")
if err != nil {
    panic(err)
}
{% endhighlight %}

We still have to convert our array of bytes into numbers that we can perform mathematical operations on and compare, so we'll create a function to do that.

To summarize this function, we're inputting a byte array, converting the byte array into a single string, splitting the string by line, then creating an integer array of the same size as the number of lines.

Then, we're looping through each line and checking to make sure that the line isn't empty. After, we're converting each line from a string to an integer and checking that there are no errors. Finally, we're setting the value of our current index in the integer array to our new integer value and then returning the integer array once the loop is completed.

{% highlight go %}
// ConvertByteArrToIntArr converts a byte arr to int arr
func ConvertByteArrToIntArr(b []byte) []int {
	lines := strings.Split(string(b), "\n")
	nums := make([]int, len(lines))
	for i, l := range lines {
		if len(l) == 0 {
			continue
		}
		n, err := strconv.Atoi(l)
		if err != nil {
            panic(err)
        }
		nums[i] = n
	}
	return nums
}
{% endhighlight %}

We could do this in a less explicit way using `bufio`, Go standard library's buffered I/O, but one of my favorite parts about Go is just how explicit it is.

## Categorizing our numbers

Now that we have a single array of integers that represents our input, we need to separate them into two groups, one being higher than half of `2020` and one being lower.

{% highlight go %}
// SplitIntArrToUpperAndLower splits an array into two arrays that represent the above and below a target
func SplitIntArrToUpperAndLower(target int, ints []int) ([]int, []int) {
	lower := make([]int, 0)
	upper := make([]int, 0)
	for _, val := range ints {
		if val <= target {
			lower = append(lower, val)
		} else {
			upper = append(upper, val)
		}
	}
	return lower, upper
}
{% endhighlight %}

This function splits a single array into two and conditionally putting each integer into the upper or lower array. As a bonus, the function input is a sorted array, both of the output arrays will also be sorted.

## Finding our pair of numbers

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

This solution would work on small inputs, but would quickly take too long to compute as the compute-time would grow exponentially at a rate of [O(n^2)](https://developerinsider.co/big-o-notation-explained-with-examples/) compared to a linearly-growing input.

<img class="image" src="https://lukasmestan.com/assets/images/o-n2.png" alt="O(N^2)">
<figcaption class="caption">Just look at this graph.</figcaption>

In other words, this is not a scalable solution and would quickly take too long to compute if the size of our input was large. Fortunately for us, with this problem it's not, so we end up with our pair of numbers and can use this to quickly end up with a solution.

**Binary Search**

Generally, a well-accepted alternative to linear search is using binary search.

To summarize, rather than search each object one by one to find a match, we take a sorted array and compare the item we're looking for with the object in the middle of the array. Depending on whether our object is greater than or less than the middle, we throw away the known incorrect half of the array and repeat the search using the correct half of the array.

If we are searching for values, we have to make one side equal to the other. We can do this by creating a `Map` function which we'll use to return custom values of the items in one of our arrays. We'll utilize this similar to a lambda in Python, which is an unnamed function, where we're subtracting.

In this example, `subtracted` is an array of integers where the values are the difference between 2020 and the lower number. Because the lower number plus the higher number equals `2020`, the `subtracted` array can be used in our binary search to find the same item in both.

{% highlight go %} 
subtracted := Map(lower_array, func(i int) int { return 2020 - i })

// Map the result of a function to each value of an int arr
func Map(input []int, f func(int) int) []int {
	result := make([]int, len(input))
	for i, val := range input {
		result[i] = f(val)
	}
	return result
}
{% endhighlight %}

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

## Hashmaps

Go's built-in `map` implements a hash table, which for integers means that it would greatly improve our lookup time to find matches.

This improves our pair-finding from O(N^2) -> O(logN) -> O(1) effectively.

I have not yet implemented this solution.

## Benchmarks

A quick benchmark with our input comparing our naive approach above to Go package `sort`'s `Search()` binary search implementation, we can see that:

```
Linear: 443ns
Binary: 1.299µs
```

The benchmark shows unexpected results in favor of Linear search. After closer examination, it's likely that my benchmark was flawed in a few ways:
1. The time it takes to sort was not factored into Binary's result, as the arrays for both had already been sorted
2. Multiple benchmarks were not done (ie: the input order didn't change), so it's possible that the binary search was worst case vs linear best case considering that both benchmarks were trying to find a single pair of matching values in two arrays


