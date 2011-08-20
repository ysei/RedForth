A Brief Introduction to Forth
This description is copyright 1993 by ACM, and was developed for the Second History of Programming Languages Conference (HOPL-II), Boston MA.
Philip J. Koopman, Jr.
koopman@cmu.edu


Forth is both an extensible language and an interactive program development methodology. Originally developed for small embedded control mini- and micro-computers, Forth seems to have been implemented on every major processor manufactured. It has been used in a wide variety of applications, including spreadsheets, expert systems, multi-user databases, and distributed real time control systems.

Forth is an extensible language and REPL program development methodology.

Originally developed to control mini- and micro-computers, Forth has been implemented in every major processor manufactured and seemingly every language under the sun - Spreadsheets, Expert Systems, Databases and some brief Artificial Cognition work.

## Two-Stack Abstract Machines

At its most facile, Forth is a language for representing a stack-based abstract machine. In essence, the Forth abstract machine has a program counter, memory, data evaluation pulldown stack, ALU and the perennially popular subroutine return address pushdown stack.

Data evaluation follows Lisp-style conventions, better known amongst the Hacker News crowd as postfix notation.

Consider the following,
```forth
3 4 + 5 * .
```
You can see that it pushes the integer value 3 to the stack, pushes the value 4 on top of 3, destructively added 3+4 to get 7, pulls down 5 from the local stack frame and multiplies by 5. The '.' operation displays the top stack element. Of course as you know this is one massive lie on the operating system's behalf because all of these, in reality, operate on different callstacks and only give the illusion of the perfect computer science world and are just simply cheap EBP (The Base pointer of the local stack frame) operations chained together. ho-hum.
## Factor your shit
On a more abstract level, Forth use RPN not necessarily to achieve a direct goal, but rather as a means for simple syntax. Small, simple programs perform complex functions that are written by reusing common code sequence through factoring.
Subroutine calls and returns are an important part of Forth programs and the factoring process. As an example, consider the following function (called a word in Forth) which computes the sum of squares of two integers on top of the Data Stack and returns the result on the Data Stack:
>  : SUM-OF-SQUARES   ( a b -- c )   DUP *   SWAP   DUP *  +  ;

The Data Stack inputs to the word at run-time are two integers a and b. The Data Stack output is a single integer c. The : denotes a function definition with the name SUM-OF-SQUARES. The ;terminates the definition. Comments are enclosed in parentheses. This example follows the Forth convention of including a stack-effect comment showing that a (the second stack element) and b (the top stack element) are consumed as stack inputs, with c produced as the stack output.
By the process of factoring, the example program would be re-written in Forth using a new definition (a factor) called SQUARED to allow sharing the common function of duplicating and multiplying a number on the Data Stack. The separation of the Return Stack from the Data Stack in the abstract machine allows the values on the Data Stack to be cleanly passed down through multiple levels of subroutine calls without run-time overhead. In this new version, Data Stack elements are implicitly passed as parameters from SUM-OF-SQUARES to SQUARED:
> : SQUARED   ( n -- nsquared )     DUP *  ;
> : SUM-OF-SQUARES   ( a b -- c )  SQUARED  SWAP SQUARED  +  ;

Good Forth programmers strive to write programs containing very short (often one-line), well-named word definitions and reused factored code segments. The ability to pick just the right name for a word is a prized talent. Factoring is so important that it is common for a Forth program to have more subroutine calls than stack operations. Factoring also simplifies speed optimization via replacing commonly used factors with assembly language definitions. In the preceding example, SQUARED could be re-written in assembly language for speed while maintaining the same stack effects.
Writing a Forth program is equivalent to extending the language to include all functions needed to implement an application. Therefore, programming in Forth may be thought of as creating an application-specific language extension. This paradigm, when coupled with a very quick edit/compile/test cycle, seems to significantly increase productivity. As each Forth word is written, it can be tested from the keyboard for immediate programmer feedback. For example, the definitions above could be summarily tested with:
> 3 SQUARED .   9 ok
> 3 4 SUM-OF-SQUARES .   25 ok
