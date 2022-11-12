# Observations when learning PRQL Language

The files in this repo are small PRQL sample files. I copy/paste the results into my DB manager to verify the queries.
There are also remnants of experiments with `prql-query` and `PyPrql`.

The following scattershot comments came from my attempt to replicate the queries of
[my repo of property tax values in my town.](https://github.com/TaxFairness/TaxFairness/blob/main/README.md).
(Don't worry - this is all public information,
created from public Town documents.)
Its README.md file has a number of queries against the **Property\_In\_Lyme.sqlite** database file.

I realize that the parser and other facilities are not complete.
However, they seem to be good enough to criticize.

I must remind the reader that I don't know much at all about SQL.
All my experiments are empirical - Does this work? How 'bout now? So don't take any of this as a deep insight.

I have also not been especially attentive to reading the docs. Some of these notes may hint at documentation tweaks.
Other questions may just come from the fact that I
don't know the "correct name" to look up what I want.

**Update:** As noted in https://github.com/prql/prql/issues/1117,
these comments fall into several categories:

* The VSCode Extension for PRQL v0.3.2 is a terrific learning environment.
I will note that it's easy to generate valid-looking PRQL that isn't valid SQL.

* Functions and variables (as touted) are really
helpful for making the PRQL code cleaner.
The simplification of "SQL syntax"
makes it feel easier to write queries, even if the PRQL parser is not terribly forgiving/informative.

## Better Documentation

* Am I correct that the opening `[` must be on same line as the `select` or `derive` for a multi-line list? It would be helpful to  examples showing lists in brackets each on separate line.

* Similarly, I was unsure how to set multiple 
`ON...` expressions for a join.
I ultimately said, "Well, maybe they just go in a list
`[ ... ]`.
I tried it and it worked. I don't know how you could make it more obvious - maybe another example in the JOIN documentation?

* How to specify the correct join columns if different tables have different/conflicting names? In SQL, I would use `...from Table1 l join Table2 r on l.foo = r.bar`

* Your example `celsius_of_fahrenheit` of really grinds my gears. Can you use the correct formula?

   ```
   func celsius_of_fahrenheit temp -> (temp - 32) * 5/9
   ```
   
* Use comments more prominently in examples.
Hmmm... I say this because I felt the need to look up the comment syntax.
I now realize that many lines in the examples have
on-the-line comments - perhaps some full-line comments would be useful in examples?

* I felt the urge to move a common expression out of the `SELECT...` list (so it wouldn't be in its own column, yet avoid duplicated code). AHAH! That's what `derive` is for - creating a variable that isn't part of a table. Correct? **Yes**

* What's the difference between `derive` and simply assigning a variable name in a `select`? Is it the ability to assign a name outside a `select`?
**Update:** Yes, I think so.

* What's the rule for using `x` in functions vs `{x}`?
(Maybe it has to do with s-strings...)
**Update:** Yes. Functions work as expected when using the direct name of the parameter.
F-strings or S-strings require `{}` around the parameter name to substitute it.  
* Why do some S-strings use `"` and others use `"""`?
I don't find an explanation in the Book. **Update:** `"`, `"""`, `'` and `'''` as well as wrapping strings in "the other" quote
(e.g.`'looking for "foo"'`) all work for S-strings.

* I didn't initially realize that it is possible to assign a "column heading" as a side-effect of a calculation: `heading_name = <expr>` Perhaps better documentation


## Bad error messages
 
* Lack of `"""` at end of S-string gives an inscrutable message: `expected interpolate_string_inner_literal` And worse, here is an example showing the message on the wrong (first) line:

   ```
   func func1 d -> s"""printf("$%,d",{d}) as {d}"""
   func func2 d -> s"""printf("$%,d",{d}) as {d}   
   ```
   
* Leaving off a `","` at the end of a select line gives a vague error: `expected operator_mul, operator_add, operator_compare, operator_logical, or operator_coalesce`. Perhaps you could special case this message to "Missing a comma?"

## Bugs

* Must `filter` must precede `select`?
I don't understand the semantics.
`pq8.prql` shows an error if `filter` is at the end, but works OK if `filter` comes before the `select`. Why?

* And the error in `pq8.prql` is _Unknown variable_ when it's clear that it has been used prior.
**Update:** This is an example of `filter` after `select` causing an error

* `pq14.prql` documents the [reported bug](https://github.com/prql/prql/issues/1116) for sub-expressions.

* In this example, there's an extra space before the `"-"` in the generated code. Is that expected?

   ```
   func pct o n -> s"""cast((100*({n}-{o})/{o}) as real)"""
   yields
   cast((100 *(125 -100) / 100) as real)
   ```
   
* Are variable names case-sensitive?
Sometimes it seems they are, although I didn't write down an example.
And sometimes VSCode extension declared that a variable was not defined when I had clearly used it earlier. How does it know?
**Update:** See #1125 for a bug report.
See also the `pq21.prql` file for an example
