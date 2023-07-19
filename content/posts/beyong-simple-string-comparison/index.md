+++
author = "Harsh"
title = "Beyond Simple String Comparison"
date = "2023-06-20"
description = "Beyond Simple String Comparison"
+++

String comparison is a common task that we all encounter in programming, and most programming languages have built-in methods to help us accomplish this. However, as we delve into more complex use cases, string comparison can quickly become challenging, and the simple comparison algorithms we're used to can quickly fall apart.

In this post, we'll look at case-insensitive string comparison in Python and learn about the more advanced methods that are necessary for tackling some complex cases.

## String Comparison

In Python, we use the equal operator (==) to compare strings, and as we know, this operator does a case-sensitive comparison, for example

```python
"string" == "String"
>>> False
```

The most common way to do a case-insensitive string comparison, and something you might be used to doing, is by converting both strings to either uppercase or lowercase and comparing the returned values, for example

```python
"string".lower() == "String".lower()
>>> True
```

This works, and you would be fine using this method for simple use cases however there are two issues with this method,

1. The `.lower()` and `.upper()` methods in Python use a technique called **string mapping** which converts a string to either lowercase or uppercase and by convention, these methods should primarily be used for display purposes.
2. A more practical issue with using these methods for string comparison is that they would simply fail in some cases and produce incorrect results. Let's look at an example,

   ```python
   string1 = "der Fluß"
   string2 = "der Fluss"

   # converting string1 and string2 to lowercase and comparing the returned values
   string1.lower() == string2.lower()
   >>> False
   ```

   > ß is a German letter that can be written as "ss" in English.

   Both _string1_ and _string2_ are case-insensitively equal however the comparison above returns **False**. This is because the `.lower()` method cannot properly normalize non-ASCII characters, thus failing to compare "ß" with "ss".

In this case, we can use the `.casefold()` method provided by Python which identifies Unicode characters in a given string and converts them to lowercase.

```python
string1 = "der Fluß"
string2 = "der Fluss"

string1.casefold() == string2.casefold()
>>> True
```

Since `.casefold()` identifies a wide range of characters, the normalization of "ß" is handled properly, and we get the expected output.

As a rule of thumb, use `.lower()` when displaying the lowercase version of a string, and use `.casefold()` when doing a case-insensitive string comparison.

### `.casefold()` is not perfect

The `.casefold()` method we looked at will work in most cases however when we start dealing with more complex strings, this method will start producing issues. Here's an example of such a case,

```python
string1 = "ŝ"
string2 = "ŝ"

string1.casefold() == string2.casefold()
>>> False
```

Surely both _string1_ and _string2_ are the same characters so the comparison between them should equate to true, but that's not the case, so what's going on?

The string comparison above fails because the strings assigned to _string1_ and _string2_ appear to be the same, but they both are constructed with different Unicode encodings. The string assigned to _string1_ is a single Unicode character, whereas the string assigned to _string2_ is constructed by combining two Unicode characters. Let's prove this by printing the length of _string1_ and _string2_.

```python
string1 = "ŝ"
string2 = "ŝ"

f"string1 length: {len(string1)}, string2 length: {len(string2)}"
>>> 'string1 length: 1, string2 length: 2'
```

Let's look at what these characters are using the `unicodedata` module.

```python
import unicodedata

string1 = "ŝ"

unicodedata.name(string1)
>>> LATIN SMALL LETTER S WITH CIRCUMFLEX
```

```python
import unicodedata

string2 = "ŝ"

# iterating over string2 as the string contains two characters
[(unicodedata.name(c)) for c in string2]
>>> LATIN SMALL LETTER S
>>> COMBINING CIRCUMFLEX ACCENT
```

We see that _string1_ is the letter "s" with circumflex and _string2_ is a combination of the letter "s" and a circumflex accent.

We can also construct these characters ourselves in Python using their Unicode encodings.

```python
"\u015d" #Unicode for the latin small letter s with circumflex
>>> 'ŝ'

"\u0073\u0302" #Unicode for the latin small letter s combined with circumflex accent
>>> 'ŝ'
```

We now know that both _string1_ and _string2_ not only just appear to be the same characters, they indeed are the same characters, merely constructed with different Unicode encodings. This means that we would also want them to equate to each other, and to achieve this, we will use something called **Normalization Form Canonical Decomposition** **(NFD)**. NFD decomposes a Unicode string into its constituent characters such that each character is in its canonical form.

In our case, the string "ŝ" would decompose into Unicode characters "U+0073" and "U+0302". Let's look at how we might do this in Python.

```python
import unicodedata

string1 = "ŝ"
string2 = "ŝ"

string1 = unicodedata.normalize('NFD', string1) # string1 is decomposed into "\u0073\u0302"

string2 = string2 = unicodedata.normalize('NFD', string2) #string2 is decomposed into "\u0073\u0302"

string1.casefold() == string2.casefold()
>>> True
```

In the above code snippet, we used the `.normalize()` method provided by the `unicodedata` module to decompose the _string1_ and _string2,_ into the combination of two Unicodes (U+0073 and U+0302). The comparison between _string1_ and _string2_ equates to true since they both now are the same string with the same Unicode encoding.

## Conclusion

In most cases, we would be fine using the `.casefold()` method for case-insensitive string comparisons but depending on the characters and languages we're dealing with, we might need to turn to more advanced techniques as we did in the last example.

While it's not possible to cover every single use case in a single blog post, I hope that the information provided will serve as a solid foundation for your future research. Remember, every project and situation is unique, so don't hesitate to dive deeper and find the right solution for you.
