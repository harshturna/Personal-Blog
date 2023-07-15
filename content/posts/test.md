---
title: "Concurrency is not all that great"
description: "Hugo, the world's fastest framework for building websites"
date: "2019-02-28"
---

# Randomize iterables in Python using sorted()

One of the most useful built-in functions in python is the sorted() function, which takes in an iterable and returns a new list containing all the items from the iterable in ascending order.

> sorted() can take in any iterable (list, tuple, set, etc.), and it will always return a **list**.

sorted() also takes in a less-used optional parameter _key_, which accepts a function and customize the sort order based on the function. Let's explore this parameter in this post.

### key parameter in the sorted() function

The key parameter takes in a function, which gets applied to each element of the iterable, and each element gets an associated value as the result of the function evaluation. The sort then happens based on that associated value instead of the actual element.

**Example**

- Let's start by creating a list of some uppercase and lowercase letters

```python
letters = ['a', 'D', 'c', 'B']
```

- Now, let's call our sorted function on this list.

```python
sorted(letters)

> > > ['B', 'D', 'a', 'c']
```

We might have expected dictionary ordering (i.e. 'a' before 'B', and 'c' before 'D'), but that did not happen because the letters are sorted based on their character codes, and in lexicographical ordering, uppercase letters precede lowercase letters.

> We can use the ord() function in python to get the Unicode of a specified character. For example, ord('A') == 65, and ord('a') == 97.

#### What if we want to ignore the case when sorting letters?

This is where the key comes to the rescue. We can pass a function to the key which would associate a case-insensitive value to each element, and that value would be used to sort our list.

```plaintext
letters = ['a', 'D', 'c', 'B']
sorted(letters, key=lambda letter: letter.lower())
```

Now, when we look at our results, we get a case insensitive result because the sort was done on the lower case value of each element.

```plaintext
letters = ['a', 'D', 'c', 'B']
sorted(letters, key=lambda letter: letter.lower())
>>> ['a', 'B', 'c', 'D']
```

### Having fun with sorted()

We can pass in a sorted iterable to the sorted() function and use key to create a randomize list.

```plaintext
import random
sorted_list = [1,2,3,4,5,6,7]
sorted(sorted_list, key=lambda num: random.random())
```

let's look at the output,

```plaintext
import random
sorted_list = [1,2,3,4,5,6,7]
sorted(sorted_list, key=lambda num: random.random())
>>> [3,4,2,6,1,7,5]
```

We used the random() function from the random library to generate a random number for each element of the sorted_list, and sorted() function used these random numbers to sort the elements. Since the generated numbers are random, the sorted function randomized our list elements.
