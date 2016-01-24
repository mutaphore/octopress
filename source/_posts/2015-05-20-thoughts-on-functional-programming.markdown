---
layout: post
title: "Thoughts on Functional Programming"
date: 2015-05-20 00:38:01 -0500
comments: true
categories: 
- Programming Languages
---

This past semester my Programming Languages class exposed us to several functional languages: Lisp/Scheme, SML and Scala (to a lesser extent). Before this exposition I really had no clue about what functional programming is. FP is a ubiquitous term used in all software/cs talks, most of the time I’ll just pretend to know what people are talking about. However, quickly I will realize that knowledge acquired from a 5 min wikipedia read just barely scratches the surface on this topic.

<!-- more -->

What is FP (Functional Programming)?
=====
A programming language is the medium in which we communicate with the computer and instruct it to do certain tasks. There are many ways to write a program, just as there are many ways to write an essay. Functional programming is, on very simple terms, a style of writing a program. The other major way is the imperative style, which is the more easily understood and most widely used (which we will not talk about here). The difference between imperative and functional programming boils down to the following:

- Imperative - tell the computer how to do something
- Functional - tell the computer definition of something

So imperative style is the “How” and functional style is the “What” of doing something. In the classical problem to calculate the factorial of number, with an imperative style, the pseudocode would be following:

```
fac(n):
    res = 1
    for i = 1 to n:
        res = res * i
    return res
```

Now for functional style:

```
fac(n):
    if n == 0 or 1:
        return 1
    else:
        return n * fac(n - 1)
```
If we were to translate the program into words, the imperative style factorial would be:

“To calculate factorial of n, first start with a result variable that holds 1, then repeatedly multiply it by the next larger number 2, 3, etc. until you reach the n value. Each time you multiply save the result of the product back into the result variable. After all n numbers are multiplied, just return the result.”
The functional verbal version would be:

“By definition, factorial of 0 or 1 is always 1. Otherwise, Factorial of a number is just the product of that number with the factorial of a number that is 1 smaller.”
In an imperative style, we explicitly tell the computer that the steps that it needs to take to reach a goal by using “imperatives”, i.e. an instruction sequence. In a functional style, we give it the precise definition of what it is that it needs to compute. Functional programming is like telling the computer the math formula for doing something and then let it handle the rest.

Which universe are you in?
=====
## Loops
The presence of any kind of looping construct in a program means that it belongs in the “imperative universe”. This is because when we are talking about loops, we are essentially thinking about the sequential steps of doing something instead of the definition of something. In purely functional languages, there are no loop constructs so it forces the programmer to use recursion. This was probably one of the hardest thing to grasp for me when I first started programming in functional languages like Scheme or SML.

For example, how do we write a function to sum up all the elements in an array A, given its length n? Assuming there is at least 1 element in the array, the logic of an imperative style is a piece of cake:

```python
def sum(A, n):
    total = 0
    for i = 1 to n:
        total = total + A[i]
    return total
```

For the functional style:

```python
def sum(A, n):
    if n == 1:
        return A[0]
    else:
        return A[0] + sum(A[1:], n - 1)
```

Notice that the functional style did not use any loops at all. The definition provided by the functional style program is essentially saying:

“The sum of a 1-item array is simply value of the only item in the array. Otherwise, the sum is the first item in the array plus the sum of the rest of the elements in the array.”
Now read that over a few times, isn’t that exactly what the definition of an array sum?

## States
Another reality test between imperative and functional is the presence of states. In the previous examples, note the occurrence of the res and sum variables in the imperative style that were used to keep track of the progress of the function. Without these state variables we have no way of knowning when to finish and return the answer. This is the pattern of saving state in an imperative programming style. In contrast, functional styles do not care about states because the program is not written in sequential imperatives. If a functional program correctly defines the problem in its entirety, it will return the correct result without needing to keep track of the running state.

In the functional universe, lack of state also means lack of mutable variables. We no longer need to define variables that mutates or changes to help us keep track of some state. This is why functional style centers on the paradigm of immutability. Immutability also translates to not needing to make any assignment to variables, because if we assign something we change it, if we change it then we destroy the functional aspect of the program. Thus, immutability is closely related to lack of the concept of assignment in a program.

Functional programming languages such as Scheme does not have the assignment operator, forcing the programmer to code in a functional manner:

```scheme
; Remove element k from list L
(define (rem L k)
  (cond ((= k 0) (cdr L))   ; '=' here means equality and not assignment!
        (else (cons (car L) (rem (cdr L) (- k 1))))))
```

Languages such as ML have the assignment operator to create a val. But once a value is assigned something, it cannot be changed later:

```
(* Bubbles smallest value to beginning of list *)
fun bubble [] = raise listError
 |  bubble [x] = [x]
 |  bubble (x::xs) = 
    let val (y::ys) = bubble xs
    in if y < x then
       y::x::ys
       else
       x::y::ys
    end
```

In line 5, val (y::ys) = bubble xs binds (y::ys) to the result of calling bubble recursively. Once bound, its value cannot be changed further.
