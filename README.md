# Monolog

Monolog is a simple logic programming language. It's syntax is a subset of the Prolog.

It is available within this REPL.

_____

To run: `$ ruby main.rb`


## How to (work with the REPL):

### To store a fact or rule in the knowledge base

Make sure you are in the storing mode by inputting `:s` or `:store` and hitting enter.
Now you can insert predicates and fact one by one into the knowledge base.


### To show the whole knowledge base

In any mode input `:show` and the whole knowledge base will be printed one fact/rule per line.


### To clear the whole knowledge base

In any mode input `:clear` and the whole knodledge base will be cleared.


### To check a validity of the term

First switch to the checking mode by inputting `:c` or `:check` and hitting enter.
Now you can submit a term in the form of lone "predicate query" like `foo(x)` or conjuction of terms like `foo(x), bar(y)`.


### To control the flow of the evaluation

When querying terms, which can backtrack and may produce more than just one answer use `:n` or `:next` to search for the next answer and `:d` or `:done` for concluding the search.

___

## Examples

You can load following fragment of the knowledge base into the REPL.

```prolog
plus(z, N, N).
plus(s(N), M, s(R)) :- plus(N, M, R).
times(z, _, z).
times(s(N), M, A) :- times(N, M, R), plus(R, M, A).
fact(z, s(z)).
fact(s(N), R) :- fact(N, PR), times(s(N), PR, R).
```

Now switch to the `checking mode` and try following queries.

Computing the factorial of 4 using Peano numbers:

`fact(s(s(s(s(z)))), R).` should produce

```prolog
  R = s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(s(z))))))))))))))))))))))))
```

Of course 24 of `s`'s are a bit hard to count, so maybe something bit smaller:

`fact(s(s(z)), R).` should produce
```prolog
  R = s(s(z))
```

Computing factorials is interesting as well as important, but it doesn't really manifest the power and beauty of the logic programming. We can try a different example, we can ask which number is the same as it's factorial.

`fact(A, A).`

And `Monolog` will promptly answer with `A = s(z)` and if we ask him politely to try and find another answer it will produce `A = s(s(z))`. That should be enough and we should instruct `Monolog` to conclude this query. There is no other number which would satisfy our condition anyway.

This was, without a doubt, quite enlightening but we can go even further, we can ask `Monolog` to find all the pairs of numbers such that they are in the `fact` relation as we defined it above.

We input `fact(A, B).` and it should feed us the following.
```prolog
  A = z
  B = s(z)
```

We are now free to ask for another pair from the `fact` relation and we will be greeted with

```prolog
  A = s(z)
  B = s(z)
```

We can go on infinitely or until the search for a single answer takes longer then couple of moments, which will be roughly around the factorial of the number 5 or 6 depending on your hardware. *Our infinity is quite small, but it's all that we got.*

___

We can also try something bit simpler like `plus(A, B, B).`

This should produce
```prolog
  A = z
```
Meaning that this will be satisfied when the first number will be zero and the second number doesn't matter - it can be whatever. *But it must be a number still.*

But if we ask the `Monolog` for another answer to that query something strange will happen - it will dive into the unbounded recursion and quickly consume the whole stack and cause `stack overflow exception`. This behaviour, while not unexpected, is different from what `SWIPL`, for example, does. The `SWIPL` doesn't do strict **occurs** checking by default, but `Monolog` does exactly that, so the result of that one quiery will differ between these two.

Because the benevolent unification of the `SWIPL` produces some interesting results the `Monolog` does have an option to disable **strict occurs checking**. You simply input `:o` or `:occurs` and it togles the occurs setting.

What that means is you can load a fact like this one.
```prolog
one(X, s(X)).
```

and test it, first with **strict occurs check enabled**
```prolog
one(A, A).
```

This should produce `False` and nothing else.

But when we disable the **strict occurs checking** we will get this extra answer.
```prolog
  A = s(A)
```
This is obviously incorrect and breaks all kinds of rules, but `SWIPL` defaults to that behaviour so `Monolog` offers it too. So should you enjoy your answers incorrect and recursive, `Monolog` got you covered.

____

## Short syntactic bootcamp *should you need it*

We have all kinds of literals:
- Strings like `"hello world"`
- Numbers like `23`
- Atoms like `peanut`

Atoms are words which always start with a lower case letter.

We also have variables like `A` or `B`. They start with upper case letter.

We have facts:
```prolog
one(s(z)).
```

This makes the `one` a fact. It also means that `one` only accepts `s(z)` as an argument.

For a fact to unify with a term they must have a same name, same number of arguments and values of arguments must unify with corresponding patterns in the definition of the fact.


For anything more complicated we can use rules:
```prolog
even(s(N)) :- odd(N).
```
Here `even` is a rule. It has a **head** which is the name and list of arguments and a **body** which is made of `odd(N)` and ended with the dot symbol.

For the rule to unify with a term they must have the same name, same number of arguments, arguments must unify with patterns in corresponding positions and a body must unify within the same context.


To express that something should be true **AND** some other thing should be true also, we need to use the `conjunction`. Conjunction is written as `,` in the `Monolog` *same as in Prolog*. So we can do something like:
```prolog
longAndBlue(A) :- long(A), blue(A).
```
Assuming we define `long/1` and `blue/1` we can make something out of it.

> Name of the predicate followed by the slash and a number is a convention in the Prolog to refer to that definition of the predicate of the given name, which has *that many* arguments.
