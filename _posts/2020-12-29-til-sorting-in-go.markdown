---
title: "TIL: Sorting in Go"
layout: post
date: 2020-12-29 11:00 PM
tag:
- go
- ds&a
- til
hidden: false # don't count this post in blog pagination
description: "How to use Go's sort package to sort data"
category: blog
author: randykinne
externalLink: false
---

## Context

Searching and sorting are common algorithms in software. My recent journey into Golang and [Christmas-themed algorithms](https://randykinne.dev/blog/2020/12/16/deep-dive-advent-of-code-day-1-in-go.html) has involved exploring both concepts and their implementations. One of my favorite features of Go has been the high quality built-in packages (namely `testing` and `net/http`), and Go's `sort` package is no exception.

## Usage

We can sort any data type in Go using the `sort` package. For example, we have an array of integers named `s`. A call to `sort.Ints()` will sort `s` using [quicksort](https://golang.org/src/sort/sort.go?s=5416:5441#L216).
```go
s := []int{4, 2, 3, 1}
sort.Ints(s)
fmt.Println(s) // [1 2 3 4]
```
A similar function call could be made to sort an array of strings:

```go
s := []string{"b", "a", "d", "c"}
sort.Strings(s)
fmt.Println(s) // ["a" "b" "c" "d"]
```

 Both (and others for standard data types) wrap an underlying generic function called `sort.Sort()` which accepts an interface as input. 
 
 We can use `sort.Sort()` to sort custom data structures, as long as they are collections that implement the `sort.Interface` interface:

 ```go
type Interface interface {
        // Len is the number of elements in the collection.
        Len() int
        // Less reports whether the element with
        // index i should sort before the element with index j.
        Less(i, j int) bool
        // Swap swaps the elements with indexes i and j.
        Swap(i, j int)
}
 ```


