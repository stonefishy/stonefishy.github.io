---
layout: post
title: "Guava - Ordering"
date: 2014-12-12 17:28:07 +0800
comments: true
categories: [Guava, Java]
---

`Ordering` is Guava's fluent Comparator class and implements Comparator interface. It can be used to build complex comparators and apply them to collections of objects. 

Okay, so let's getting started. Firstly, we declared a `String` type List:   
``` Java
    private List<String> locations = Lists.newArrayList(
            "Guang zhou",
            "Cheng du",
            "",
            "Shang hai"
    );
``` 
We will use this locations variable later.
<!-- more -->

`natural()`   
Use natural ordering for comparatable types. such as number from small to large. date from start to end. It is static method.   
``` Java
    // use natural comparator, such as a -> z
    // so the result is: [, Cheng du, Guang zhou, Shang hai]
    System.out.println(Ordering.natural().sortedCopy(locations));
```

`sortedCopy(Iterable)`   
Return a sorted copy of the specified elements as a List.    
We create a customized `Ordering` that sorted by the length of element from small to large. Let's get the code:
``` Java
    private Ordering<String> getCustomzieStringOrder() {
        return new Ordering<String>() {
            @Override
            public int compare(String first, String second) {
                return Ints.compare(first.length(), second.length());
            }
        };
    }
```
Order the locations list with our new customize `Ordering`:   
``` Java
	Ordering<String> byLengthOrdering = getCustomzieStringOrder();

    // use natural comparator, such as a -> z
    // so the result is: [, Cheng du, Shang hai, Guang zhou]
    System.out.println(byLengthOrdering.sortedCopy(locations));
```

`isOrdered(Iterable)`   
Return a Boolean variable that can describe the Iterable if sorted.
``` Java
	// will return false. because the locations array is not sorted.
    System.out.println(byLengthOrdering.isOrdered(locations));
```

`reverse()`   
Return the reverse `Ordering`.
``` Java
    // reverse customize comparator.
    // so the result is: [Guang zhou, Shang hai, Cheng du, ]
    System.out.println(byLengthOrdering.reverse().sortedCopy(locations));
```

`nullsFirst()`   
Return an `Ordering` that will put the null object at the first.
``` Java
    // add null object to the locations array.
    locations.add(null);

    // null obejct will at first.
    // so the result is : [null, , Cheng du, Shang hai, Guang zhou]
    System.out.println(byLengthOrdering.nullsFirst().sortedCopy(locations));
```

`nullsLast()`   
Return an `Ordering` that will put the null object at the last.
``` Java
	// null object will at last.
    // the result is : [, Cheng du, Shang hai, Guang zhou, null]
    System.out.println(byLengthOrdering.nullsLast().sortedCopy(locations));
    
    // will throw NullPointerException, because the location contains null object.
    // and here doesn't have nullsFirst() or nullsLast() to deal with null object.
    byLengthOrdering.sortedCopy(locations);

    // remove the null object.
    locations.remove(null);
```

`compound()`   
Returns an `Ordering` which uses the specified Comparator to "break ties."
``` Java
    // the "AAAAAAAA" length is equal to "Cheng du" length. (8 characters)
    // and it will be appended at last.
    locations.add("AAAAAAAA");

    // use natrual ordering with second comparator to deal with equal situation.
    Ordering<String> compoundOrdering = byLengthOrdering.compound(Ordering.natural());

    // the result will be : [, AAAAAAAA, Cheng du, Shang hai, Guang zhou]
    System.out.println(compoundOrdering.sortedCopy(locations));
```

`onResultOf(Fuction)`   
Returns an Ordering which orders values by applying the function to them and then comparing the results using the original Ordering.   
Declaring a Foo class:
``` Java
	 public class Foo {
        @Nullable
        private Integer sortedBy;

        private String nonSortedBy;

        public Foo(Integer sortedBy, String nonSortedBy) {
            this.sortedBy = sortedBy;
            this.nonSortedBy = nonSortedBy;
        }

        public String getNonSortedBy() {
            return nonSortedBy;
        }

        public void setNonSortedBy(String nonSortedBy) {
            this.nonSortedBy = nonSortedBy;
        }

        public Integer getSortedBy() {
            return sortedBy;
        }

        public void setSortedBy(int sortedBy) {
            this.sortedBy = sortedBy;
        }

        @Override
        public String toString() {
            return String.format("%s+%s", this.sortedBy, this.nonSortedBy);
        }
    }
```
Declaring a new List with Foo class type:
``` Java
	private List<Foo> fooList = Lists.newArrayList(
        new Foo(null, "abc"),
        new Foo(23, "abc"),
        new Foo(10, "abc")
    );	
```
Define a new function that will return an customized `Ordering`:
``` Java
    private Ordering<Foo> getNaturalFooOrder() {
        return Ordering.natural().nullsFirst().onResultOf(new Function<Foo, Integer>() {
            @Override
            public Integer apply(@Nullable Foo foo) {
                return foo.getSortedBy();
            }
        });
    }
    
    Ordering<Foo> fooOrdering = getNaturalFooOrder();
    // the result will be [null+abc, 10+abc, 23+abc]
    System.out.println(fooOrdering.sortedCopy(fooList));
```

`greatestOf(Iterable iterable, int k)`   
Returns the k greatest elements of the specified iterable, according to this ordering, in order from greatest to least. Not necessarily stable.
``` Java
    // the result will be [23+abc]
    System.out.println(fooOrdering.greatestOf(fooList, 1));

    // the result will be [23+abc, 10+abc]
    System.out.println(fooOrdering.greatestOf(fooList, 2));
```

`leastOf(Iterable iterable, int k)`   
Returns the k least elements of the specified iterable, according to this ordering, in order from least to greatest. Not necessarily stable.
``` Java
    // the result will be [null+abc, 10+abc]
    System.out.println(fooOrdering.leastOf(fooList, 2));
``` 

`min(Iterable)`   
Returns the minimum element of the specified Iterable. Throws a NoSuchElementException if the Iterable is empty.   
``` Java
    // the result will be null+abc
    System.out.println(fooOrdering.min(fooList));
```

`max(Iterable)`   
Returns the maximum element of the specified Iterable. Throws a NoSuchElementException if the Iterable is empty.   
``` Java
    // the result will be 23+abc
    System.out.println(fooOrdering.max(fooList));
```








