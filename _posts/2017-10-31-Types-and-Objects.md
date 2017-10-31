---
layout: post
title: [C] Types and objects
---

Most types in C are what are called *complete object types*, which is to say,
they define a set of expressible values.

For example, ``int`` is a type which can express a certain range of positive
and negative integers, and ``struct int_pair`` is a type which can express
a pair of such integers, assuming that the translation unit contains a
definition like this:

```
  struct int_pair { int first; int second; };
```

(The types that aren't complete object types can be broken into several
different categories.  These differences don't really matter; suffice it to
say that they're all heavily restricted in terms of where they can appear
and how they can be used.  That's all I'll say about them here.)

Now, C is a language intended for low-level programming, so in addition
to defining a set of expressible values, a complete object type also
defines the layout of those values in memory, and certain properties of
that layout can be directly queried with language features like ``sizeof``
and ``offsetof``.  For example, ``int`` is typically stored in 4 bytes of
contiguous memory, with the bits of the integer being arranged across those
bytes in some target-specific way.

The exact details of this layout, like the size and byte-ordering of the
fundamental types and the rules for laying out ``struct`` types, are
determined the target platform's C ABI, not C as a language.  However, C
does impose some basic constraints.

When a value is validly stored in memory, it is said to be stored in an
*object*.  A declared variable creates an object of the variable's type.
A struct or array object contains *subobjects* for its fields or elements,
respectively, which are of course objects themselves.

(This use of "object" may be somewhat confusing to programmers
coming from common object-oriented languages where an "object" is an
instance of a class and has detectable reference identity.  It might be
helpful to think of it as a generalization of "variable".)

An expression in C has both a type and what I call a *value kind*, which
is basically whether the expression is an *l-value* or an *r-value*.  An
l-value is an expression that designates an object; an r-value is an
expression that produces a value not linked to an object.  For example,
if ``x`` is a variable of type ``int``, the expression ``x`` is an l-value
expression which evaluates to a reference to the object declared by
that variable, but the expression ``x + 1`` is an r-value expression
which evaluates to the result of that addition.  Both expressions have
type ``int``, but their value kind is different.  As shown in this example,
most expressions in C do not expect an l-value; in these cases the
l-value is implicitly converted to an r-value.  How this is done depends
on the type of the l-value.  Most types are converted to an l-value by
simply loading the current value from the object.  Functions, however,
are converted to a pointer r-value by taking the address of the function,
and a similar rule applies to arrays.

(Technically, a function reference in C is not an l-value because it does
not refer to an object.  However, this has no real effect, and it is
simpler to talk about function references as if they were l-values.)