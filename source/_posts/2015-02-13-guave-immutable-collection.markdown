---
layout: post
title: "Guave - Immutable Collection"
date: 2015-02-13 14:58:17 +0800
comments: true
categories: [Guava, Java]
---
`Immutable` that means it can not be changed. Let's get example to see what's different with `mutable` objects.

Normal mutable collections:   
``` Java
//define a mutable list
private final List<String> mutableList = Arrays.asList("a", "b", "c");
   
//modify the first item in the mutable list
//will print [aa, b, c]
mutableList.set(0, "aa");
System.out.println(mutableList);
```    
<!-- more -->
Immutable collections:
``` Java
//define a immutable list
private final ImmutableList<String> immutableList = ImmutableList.of("a", "b", "c");
    
//can not modify the first item in the immutable list, any change item operation is unsuppproted.
//it will throw UnsupportedOperationException
immutableList.set(0, "aa");
System.out.println(immutableList);
```    
From above example, we can get the conclusion is that immutable collections doesn't support any changing content API. So it will throw `UnsupportedOperationException` when you try to modify it.    

###What's advantage of Immutable Objects
According to Guava Offical explain about `Immutable Objects`, there have four and more advantages of Immutable Objects:

 - Safe for use by untrusted libraries.
 - `Thread-Safe`: can be used by many threads with no risk of race conditions.
 - Doesn't need to support mutation, and can make time and space savings with that assumption. All immutable collection implementations are more memory-efficient than their mutable siblings.
 - Can be used as a constant, with the expectation that it will remain fixed.

Making immutable copies of objects is a good defensive programming technique, and `Guava`provides it.

> *Note:* Each of the `Guava` immutable collection implementations reject *null* values.

###How create it
An ImmutableXXX collection can be created in several ways:

- Using the `copyOf` method, like `ImmutableList.copyOf(collection)`:  

``` Java
ImmutableList<String> result = ImmutableList.copyOf(mutableList);
```

- Using the `of` method, like `ImmutableList.of("a", "b", "c")` or `ImmutableMap.of("a", 1, "b", 2)`

``` Java
ImmutableList<String> result = ImmutableList.of("a", "b", "c");
```

- Using a `Builder`, for example:

``` Java
ImmutableList<String> result = ImmutableList.<String>builder()
                					.addAll(mutableList)
                					.build();
```

All immutable collections provide an ImmutableList view via `asList()`, so -- for example -- even if you have data stored as an ImmutableSortedSet, you can get the kth smallest element with `sortedSet.asList().get(k)`.
``` Java
//get second item. print b
System.out.println(immutableList.asList().get(1));
```