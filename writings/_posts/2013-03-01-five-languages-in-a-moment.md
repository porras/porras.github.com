---
layout: writing
category: writing
title: Five Languages in a Moment
description: A short introduction to Five different programming languages (Erlang, Haskell, Common Lisp, Clojure, and a special surprise) intended for Rubyists
---

*This article was built from the notes I used in my talk [Five Languages in a Moment](/talks/2011/five-languages-in-a-moment/) in [Conferencia Rails 2011](http://conferenciarails.org/). Not exactly a transcript of the talk but a different format for it. I invested a lot of work on that talk and found a bit sad that, once given, that work would be lost. That's why I wrote this.*

*It's an ultrafast excursion through five different languages, why are they different, and why are they remarkable. At many points, it's clearly addressing Ruby programmers (after all, it was a Ruby conference), but you might find it interesting, useful, or just fun, even if you're not one.*

## An introduction

Hello, my name is Sergio, and I love creating software. And I love Ruby, which I think is the single best language to do it.

*“Excusatio non petita, accusatio manifesta”*. If I love Ruby so much, why would I cheat on it, with no less than five different languages?

In 2005, I read this book:

[![My job went to India](/images/five/india.jpg)](http://pragprog.com/book/mjwti/my-job-went-to-india)

Its main topic, outsourcing, is kind of depressing, but it contains a lot of useful advices for your career as a software developer. One of its advices is *“learn a new language each year”*, which sounded very good to me. After all, learning is a very basic skill for our craft, and every skill can and must be trained.

So, in 2006, I learnt Ruby. Well, I mean I started to learn Ruby. You all know what happened then, because something very similar might have happened to you. Ruby changed my job, my way of thinking about the craft or programming, and my whole world. In 2007, I didn't learn any new language. Neither in 2008. Nor 2009, or 2010. Ruby is so awesome that made me forget my promise for five whole years!

![Ruby is awesome](/images/five/ruby-awesome.png)

In 2010, I remembered it and I thought I needed to catch up. What made me think so? Another book, of course:

[![Hackers and Painters](/images/five/hackers-painters.jpg)](http://www.paulgraham.com/hackpaint.html)

That's one of the most inspiring books I've ever read. Seriously, read it if you haven't. There's a concept on that book that had me thinking for days, and working on this idea for months: **The Power Continuum**.

The idea is simple: there's a power continuum between the lowest level, least powerful in abstractions language and the highest level, most powerful in abstractions one. So, why don't all programmers choose the same language, the most powerful one?. It's not so simple: as programmers, we are not able to fully know that power continuum, which remains kind of *‘platonic’* for a simple reason: we're in it. So, when we look down the stairs, all is clear for us. We see a lot of languages which are clearly less powerful than ours. We can even wonder how can that poor people get anything done in a language without, for example, blocks. Or classes.

But when we look up the stairs, things are not so easy because we may even not realize we're looking up. When we look at a language more powerful than ours, we don't see that. We just see a strange language with strange features. A language just as powerful as ours, but with some weird stuff mixed in. We simply lack the mental abstractions to understand those features. If as programmers we ‘think in Ruby’, we'll never be able to understand some powerful features present in other languages, just like a Java programmer doesn't at first understand the power of Ruby blocks. That's why having a look at those languages that seem strange to us at first is interesting: because we might find that that languages are not strange, but more powerful.

Anyway, for me, feeding new abstractions for my mind to think in sounds like a very legitimate reason to learn new languages. Even if I plan to stick with Ruby (which I do). So, as I told before, I decided to catch up with my promise.

By the time, I had just moved to live with my girlfriend. And with her, also came her huge comics collection, almost as cool as her. Next stop was a well known swedish furniture shop, to get the biggest shelve we could find, which is taller than me, and far wider. After mounting it, the miracle: an empty slot. I had to fix that! Next stop, a well known online book store. There, I found this book:

[![7 Languages in 7 Weeks](/images/five/seven-languages.jpg)](http://pragprog.com/book/btlang/seven-languages-in-seven-weeks)

I thought it was a very cool idea, and very good for my plan. But, being the compulsive book shopper I am, I came with a slightly different implementation:

![A slightly different implamentation](/images/five/books.jpg)

So, here are my findings.

<div class="logo">
  <a href="http://www.erlang.org/"><img src="/images/five/erlang.png" alt="Erlang"></a>
</div>

## Erlang

Some facts about Erlang:

* Developed by Joe Armstrong at Ericsson since 1986
* Functional language
* Concurrency oriented
* Distribution oriented
* Error tolerant

Obviously I'm not going to teach you to program in Erlang, but I'd like to explore the couple of things I liked the most about it.

### A functional language

So what is functional programming? If you don't know functional programming, I recommend you to read [this delicious article](http://www.defmacro.org/ramblings/fp.html). But if I had to summarize it, I'd tell it's **side effects free programming**. In FP, not very surprinsingly, the function is the main building block. Your program is composed of functions that, as mathematical functions, take inputs and give outputs. And that's pretty much everything. There is no global state that can be modified, just functions returning values.

This fact yields obvious limitations, and for sure requires a change in your programming mindset and techniques. Because, *how can you get anything done without changing state?*. But it also has advantages. No side effects means less bugs. And less coupling between different parts of your program. And very easy testing. And much easier parallelization.

So, variables don't vary. Doesn't seem it nonsense? I'll tell you what is nonsense:

```ruby
x = x + 1
```

That's the very first thing we all learnt when we started programming, and it goes against everything we learnt in math since our first years in school. Don't we know how equations work? X is x, not x + 1.

In Erlang, and most functional languages, = is not an assignment but an assertion. The technical name is *pattern matching*. In the most basic case, it works like an assignment:

```erlang
> X = 1.
> X.
1
```

But soon we will find a difference:

```erlang
> X = 2.
** exception error
```

For sure. X can't be both 1 and 2!

What pattern matching means is that we are asserting that both parts of the equals are actually equal, and the compiler will do whatever is possible to make my assertion true.

In this other case I'm going to assert that the variable `Point` equals a data structure composed of the symbol `point` and the integers `15` and `20`. Then I assert that the structure `{point, X, Y} = Point`. `point` equals `point`? Yes! `X` is `15`? If sou say so... `Y` is `20`? Ok!

```erlang
> Point = {point, 15, 20}.
> {point, X, Y} = Point.
> X.
15
> Y.
20
```

In most functional languages, lists are a core data type, used for everything. Erlang is no exception. In order to manipulate them, we have the concepts *head* (which is the first element of a list) and *tail* (which is the rest of the list), and a notation to get them using pattern matching:

```erlang
> L = [1, 2, 3, 4].
> [H|T] = L.
> H.
1
> T.
[2, 3, 4]
```

Ok, long enough without talking about functions. Here they are:

```erlang
double(X) -> X * 2.
```

Simple enough. Remember pattern matching? We can use it for defining function with several *clauses*:

```erlang
fact(0) -> 1;
fact(X) -> X * fact(X - 1).
```

And if you're really afraid anything can get done without variables or object orientation, here's a more powerful example:

```erlang
area({rectangle, Width, Height}) -> Width * Height;
area({square, X})                -> X * X;
area({circle, R})                -> 3.14159 * R.
```

It's much shorter and clearer than its Ruby equivalent!

```ruby
class Rectangle
  def initialize(width, height)
    @width = width
    @height = height
  end

  def area
    @width * height
  end
end

class Square
  def initialize(x)
    @x = x
  end

  def area
    @x * @x
  end
end

class Circle
  def initialize(r)
    @r = r
  end

  def area
    3.14159 * @r
  end
end
```

Well, the real equivalent is this, but I'm not sure I like it at all:

```ruby
def area(*args)
  case args[0]
  when :rectangle
    args[1] * args[2]
  when :square
    args[1] * args[1]
  when :circle
    3.141519 * args[1]
  end
end
```

There's much to functional programming, and I encourage you not to dismiss it just thinking *“bah, programming without variables, that's crazy!”*, because it has a lot of interesting ideas that can be practised in any language.

### Concurrency oriented

So, concurrency is a very interesting topic, now that Moore's law seems to have stopped and that what we're getting instead is more cores. Erlang approach is unique, and largely based on what I just told: no global state. Almost all bugs in concurrent programs are related to the sharing of some global state, a whole family of bugs which are simply not possible in Erlang.

The concurrency unit is the **process** (not in the operating system meaning of the term). Since processes share no memory or state, are easy to synchronize without using locks, and without incurring in race conditions or other subtle bugs related to thread based programming. But, if processes don't share any memory or state, how do they interact? Through message passing.

To create a new process, we use the function `spawn`, which takes a function as parameter (which can be anonymous or named) that is simply the operation to perform by that process, and which returns a process identificator that we'll need to pass messages:

```erlang
Pid = spawn(Fun).
```

Now we have two concurrent processes running at the same time, and we can have as many as we want, since creating and destroying processes is very fast, much more than in the operating system.

Which interesting things can we do with processes? Just one! Sending messages! We do it with the ! operator:

```erlang
Pid ! Message.
```

And how do we handle the messages that might be sent to our current process? With the `receive` statement, which is very similar to a `case` statement:

```erlang
receive
  Pattern -> Expression;
  Pattern -> Expression
end
```

When we issue a `receive` statement, our process checks its *inbox* for any previously arrived message and process it, and if it's empty waits for one to arrive, and process it.

One can write *adhoc* parallel programs, in which we parallelize certain calculations, but when things get really interesting is when we go generic. Let's see a example. This example may be a little complicated to explain in a minute, but I though I had to try because it's just beautiful. Don't worry if you don't get the details, try to get the *“essence”*.

We're going to implement a parallel map. It's a regular map, it takes a function and a list, and returns a new list with the results of applying that function to every element on the list. The only difference is that it applies that function in parallel.

```erlang
pmap(F, L) ->
  Parent = self(),
  Pids = map(fun(I) ->
               spawn(fun() ->
                 Parent ! {self(), F(I)}
               end)
             end, L),
  gather(Pids).

gather([]) -> [];
gather([Pid|T]) ->
  receive
    {Pid, Result} -> [Result|gather(T)]
  end.
```

We first store the main process PID, and then, using a regular map, we spawn a new process for each element in the list, that will apply the function and send the result to the main process. Just have a look at the message sent: we don't only send the result, but pack it with the PID of the child process, just like the sender name in the envelope of a letter: when we send a message, we say who's sending it, because it may be useful for the process receiving the message.

Once we have spawned a process for each element, and stored all that PIDs in a list, we just have to wait for the results to arrive. How do we do it? Using receive and pattern matching, we check or wait for a message coming from the first process and then concatenate that result recursively.

I know this example is complicated, but I found it really interesting because using generic functions is the easiest way to write parallel programs: we separate the parallelization logic from the program logic and we can get performance boosts of even xN (being N the number of avaiable cores) writing our programs like we always did, without thinking about parallelization, as long as our functions are side effects free, which in Erlang is true by definition. In a time when 2 cores are everywhere, 4 and 8 cores are fairly common, and trends seem to suggest we'll get hundreds of cores anytime soon, that's almost a superpower.

On top of that, how many times have someone told you, and you have struggled to understand, that in good OOP, *it's all about message passing*? Well, play a bit with Erlang processes and messages and I'm sure you'll struggle no more!

### Distribution oriented

But there's more. This parallelization stuff also works through different machines, in the same local network, or even across the Internet, so writing distributed programs is really easy.

### Other interesting things

* Distributed error control
* OTP: a framework which is part of the Erlang distribution. It's the end of the road I outlined when I talked about creating generic tools for doing parallelism. The goal of OTP is to let you write the logic of your application, and let for OTP the logic for parallelization, distribution, error handling, etc.
* Web development with ChicagoBoss

### You should try Erlang if...

* ...you want to play with the concept of functional programming
* ...you're interested in writing parallel programs

<div class="logo">
  <a href="http://www.haskell.org/"><img src="/images/five/haskell.png" alt="Haskell"></a>
</div>

## Haskell

Some facts about Haskell

* Developed by Simon Peyton-Jones and others since 1990
* *Pure* functional language
* Lazy evaluation
* Strict but powerful type system

So, first of all is that Haskell is also a functional language so much of the things I just told about Erlang are true about Haskell too.

But Haskell is a more *academically* functional language. Haskell lovers like to say Haskell is a niche language, and that its niche is solving hard problems. In order to that it adds a pair of things over what I just told you about functional programming in Erlang that I'd like to show you.

### Type system

* Strong: no casting or implicit conversion
* Static: at compile time
* Inferred: much of the time the compilar can infer the type of a expresion without us telling it

So, Haskell values have types. A value can be a `Char`, an `Int`, a `Bool`, etc. But that's not all, of course. First, we have composite types, like lists of values, or tuples. So all these are valid types:

```haskell
Char
[Char]
Integer
([Char], Integer)
```

There's more, we can create new types very easily:

```haskell
data BookInfo = Book Int String [String]

myBook = Book 9834756429 "Real World Haskell" ["Bryan O'Sullivan",
                                               "John Goerzen",
                                               "Don Stewart"]
```

In this case we declare that BookInfo values are created using the Book constructor and some parameters.

Or even:

```haskell
data Color = Red
           | Yellow
           | Green
           | RGB Int Int Int
           
yellow = Yellow
black = RGB 0 0 0
```

In this case we create several different constructors, each one with different parameters.

Or, as another example, the areas one we did before:

```haskell
data Shape = Circle Float
           | Rectangle Float Float
           | Square Float

area (Circle r)      = r * 3.14
area (Rectangle w h) = w * h
area (Square x)      = x * x

circle = Circle 5
rectangle = Rectangle 2 3
square = Square 1.5
```

And much more. In Haskell the idea is always being very precise about what kind of complex data is going on between functions, in order to model exactly the domain.

### More functional than any other language

Haskell goes much further with the functional approach than any other language, and functions can do almost everything. Here's an example of *partial application*:

```haskell
square_all = map (\n -> n * n)

-- > square_all [1,2]
-- [1,4]

square_all = map square
             where square n = n * n

square_all = map (^2)
```

### Lazy evaluation

We all have tried some laziness. For example, in recent versions of Rails' ActiveRecord, no database query is done until it's really needed. Just like when you told your mother you would clean up your room later. When you do:

```ruby
@articles = Article.where(:status => 'published')
```

No query is done, just the promise that it will be done later, when you access the `@article` variable in such a way that the query needs to be done:

```erb
<% @articles.each do |article| %>
<h2><%= article.title %></h2>
<p><%= article.body %></p>
<% end %>
```

So, why bother with laziness if the query is finally to be done? Because, unlike your mother, who never forgot and never forgave, you may find that you don't need to do it, or that the query you need to do is a slightly different one...

```erb
<% @articles.limit(5).each do |article| %>
...
```

Of course, there's no faster query than the one you don't do!

Haskell takes this idea really far. When you do:

```haskell
wadus 1 + 2
```

You'd expect, just like happens in any language you know, that 1 + 2 would get evaluated, and its result (3), passed to wadus. Not in Haskell. In Haskell, it's the whole expression 1 + 2 what gets passed to wadus, and **only if it needs it**, gets that expression evaluated. This example may be silly, but this allows a completely new style of programming. For example:

```haskell
iforelse cond a b = if cond
                    then a
                    else b

-- > iforelse True (1 + 2) (3 + 4)
-- 3
-- > iforelse False (1 + 2) (3 + 4)
-- 7
```

One of the three parameters never gets evaluated! In our silly example, it's a simple sum, but it could be a database query of whatever. You can pass as many parameters as you want, and only those needed are ever evaluated. Didn't you ever wished you could do that? For sure? Show me some Ruby code which is passing lambdas probably you're doing just this.

### You should try Haskell if...

* You tried the functional paradigm and want to go further
* Want to see your belief “types are useless” challenged
* You want to write extremely efficient programs in a high level language

<div class="logo">
  <a href="http://common-lisp.net"><img src="/images/five/lisp.png" alt="Common Lisp"></a>
</div>

## Common Lisp

Lisp was invented by John McCarthy... in 1958! I don't know about you, but for me the idea that this man is older than my grandfather and invented a language which is still used when my father was 5 is just mind blowing.

But leaving poetry aside, all the different Lisp dialects that emerged from that day eventually merged into two main branches, Common Lisp and Scheme. We'll have a brief travel through the first.

So, Common Lisp is almost a *normal* language, except for one or two details. It's only they're big details. The main one is the syntax, which everyone finds weird. Lots of Insipid and Stupid Parentheses, haven't you heard that? But, do you know something? All that parentheses are there for a reason, and it's a cool reason. Lisp is **homoiconic**, which, according to Wikipedia, means that *“the primary representation of programs is also a data structure in a primitive type of the language itself”*. Or simply **Code = Data**.

Here is a simple data structure in Lisp:

```common-lisp
(1 2 3 ("wadus" 5) (1.3 1.7))
```

(BTW, if parentheses annoy you, here you can see the same data structure in Ruby. You're right, it has exactly as many square brackets as the Lisp version has parentheses, and a lot of commas which are not needed in Lisp)

```ruby
[1, 2, 3, ["wadus", 5], [1.3, 1.7]]
```

Now, a fragment of Lisp code:

```common-lisp
(+ (* 3 5) (/ 10 2))
```

There's something very obvious here: Lisp uses the prefix notation, which means operators always work as functions and always come before the arguments. And yes, it is an annoyance until you get used to it. But what's really important here is: do you see any difference?

Right! There's no difference! That's what **Code = Data** means: Lisp code is not a stream of characters like in other languages, but a data structure expressed in the language itself. So, it's not that Lisp has a strange syntax but that **Lisp has no syntax**.

So, Lisp rules are as simple as this:

> *Valid Lisp code is composed of lists whose first element is the name of a function and the rest are the parameters passed*

**Now you know Lisp** because that's all.

The coolest feature of Lisp, the one I want to show you the most, is macros.

Macros generate at compile time the code than will actually get run. A.k.a. metaprogramming. Metaprogramming is a well known technique in many languages. We as rubyists like it, since it's the reason for example that Rails is so cool. But let me tell you something: no language allows you to go further in metaprogramming as Lisp. And I'll tell you why, because it's simple to understand, and, as you may have expected, it's fully related to the parentheses.

Metaprogramming is generating and manipulating code. Code in almost all languages is just a stream of characters, so to manipulate it, we have the string manipulation tools, which may be useful but are obviously limited. If you read the Rails code, most of the examples use eval or class_eval with a string built using interpolations and maybe some iteration. And sometimes that feels nasty.

In Lisp, source code is a normalized data structure: a list. All languages have powerful tools for manipulating lists, and Lisp is no exception. Actually, its name comes from LISt Processing.

A trivial and stupid example but that I think that can give you an idea about what can be possibly done with macros in Lisp:

```common-lisp
(defmacro backwards (code)
  (reverse code))

(backwards
  ("wadus" print))

(print "wadus")
```

Please note that the code used inside the backwards macro is not even valid Lisp code (remember valid Lisp code is composed by lists whose first element is a function name, which is not the case in `("wadus" print)`). We're creating a new language.

Of course, there's much more you can get done with macros and, actually, it's said that most of the code in big Lisp programs is actually macros, which is not only the dream of a hardcore metaprogrammer, but also of a lazy developer. What you're actually doing is writing a program which writes the program you need.

### You should try Common Lisp if...

* You want to take a flight in a retrofuturist jetpack
* You like metaprogramming
* You want to try one of the most abstraction-capable and still efficient languages
* You want to understand why Lisp-lovers love Lisp so much

![Retrofuturistic Jetpack](/images/five/jetpack.jpg)

<div class="logo">
  <a href="http://clojure.org/"><img src="/images/five/clojure.png" alt="Clojure"></a>
</div>

## Clojure

Clojure is Lisp meets the XXI century, in several ways:

* Lisp meets the JVM
* Lisp meets purelly functional programming
* Lisp meets lazyness
* Lisp meets concurrency
* Lisp meets human inability to keep parenthesis balanced

### Clojure is a Lisp

So, Clojure is a Lisp, so everything I just told about Common Lisp is true for Clojure too. Moreover, Clojure is a functional language, so everything I told about that is true for Clojure too.

### More laziness

Clojure is not as lazy as Haskell, but allows you to achieve similar things through *sequences* (which work just like lists, but are lazy):

```clj
(def whole-numbers (iterate inc 1))

(first whole-numbers)
# 1

(take 5 whole-numbers)
# (1 2 3 4 5)

(last whole-numbers) # => CATACROKER!
```

It's interesting how lazy sequences change the approach for some problems, making the solutions both more expressive and more performant. We are used to write functions that calculate the values we need, but in Clojure we just *define* them and then just access them using the usual list operations.

Of course, under the hood they're being calculated when we access them. Let's take for example the Fibonacci sequence, and this implementation in idiomatic Clojure:

```clj
(defn fibonacci []
  (map first (iterate (fn [[a b]] [b (+ a b)]) [0 1])))
```
      
One more time, don't worry if you don't get the details. It uses an anonymous function to calculate each pair of numbers of the fibonacci sequence, and then maps to the first of each pair, which composes the actual fibonacci sequence.

All sequence operations like map or first are lazy, so this definition doesn't calculate anything, but, still, allows us to access the sequence as if it was already calculated:

```clj
(take 20 (fibonacci))
; (0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584 4181)
```
    
This fibonacci sequence may not be what you call a solution for a problem, but you'd like to know that file reading and XML parsing (among many other things) is performed this way.

### STM

Software Transactional Memory is another approach in the quest for concurrent programming without bugs. It's a relatively new technique for storing state in a secure way. Developers used to use databases will find it familiar because it's exactly what its name suggests: wraping memory writes into transactions, which are compliant with ACI (Durability makes no sense talking about writing in memory).

Transactions are Atomic, so from *the outside* all changes will be seen simultaneously in the very moment you *commit*.

They are also Consistent, so that if you cancel the transaction, none of the changes contained will be effective.

And finally, they are Isolated, so no one can see partially completed results from other transactions.

This is a concurrency approach at a higher level than locks, and it seems a more robust one, or have you had any concurrency issues with your database lately?

So, how do you implement a memory transaction?

```clj
(def fulano-balance (ref 100))
(def mengano-balance (ref 80))

(dosync 
  (ref-set fulano-balance (+ @fulano-balance 20))
  (ref-set mengano-balance (- @mengano-balance 20)))

@fulano-balance
; 120
@mengano-balance
;  60
```

And no concurrent process ever saw `fulano-balance` with value changed to 120 but `mengano-balance` still at 80.

Being so common the operation of modifying a reference inside a transaction, we even have an abbreviated version:

```clj
(dosync
  (alter fulano-balance + 20)
  (alter mengano-balance - 20))

@fulano-balance
; 140
@mengano-balance
;  40
```

### You should try Clojure if...

* You liked both Lisp and the functional approach
* You need/want Java/JVM interoperability

<div class="logo">
  <a href="http://en.wikipedia.org/wiki/Tom%C3%A1%C5%A1_Ujfalu%C5%A1i"><img src="/images/five/ujfalusi.jpg" alt="Ujfalusi"></a>
</div>

## Ujfalusi

This is our last language today, and it might be both the most and least useful one. It's very much like Logo, but instead of a turtle, you are Ujfalusi. This means you can only turn in square angles and run, but do it blazingly fast!

Ujfalusi programs have only three valid characters:

* `>` turn right
* `<` turn left
* `-` run like there's no tomorrow

You can download Ujfalusi here: [http://github.com/porras/ujfalusi](http://github.com/porras/ujfalusi)

![Ujfalusi running](/images/five/ujfalusi_run.png)

It's true that you can't get much done with the Ujfalusi language, but, do you know what? I implemented it in very little time, and was a lot of fun.

Realizing that you can just type some lines of code and have a new language created is awesome.
Realizing that every single language, even the most used ones, was born the same way, is even more awesome.
And, as Jose Valim pointed in his Euruko talk, which I encorage you to watch if you can find the video, you can learn a lot about your current favorite languages while creating a new one, like he did with Elixir.
But over all this, it's the whole lot of fun involved in creating a language.

If you wish to play with this, there are two things you need to read. First, this wonderful ebook:

[![Create Your Own Programming Language](/images/five/create_your_language.png)](http://createyourproglang.com/)

And second, the source code of any of the number of [languages implemented over the Rubinius VM](http://rubini.us/projects), which looks very promising for this kind of task.

### You should create your own programming language if...

* You want to learn about how languages internally work
* You want to have fun doing something very geek
* Your favorite football player leaves your team and breaks your heart

## And the winner is...

The winner is **ME**. And I won for at least four reasons.

**FIRST.** Working in this talk and article, learning about all these wonderful languages was an incredibly lot of fun. Do you want more reasons?

**SECOND.** As I said before, I think learning new and different languages makes you a better programmer even if you don't end using them. You get new abstractions, new ideas, new styles, and a wider view about the craft of programming.

**THIRD.** That *“even if you don't use it”* is boring, I might have heard that a million times. That might have been good for 1995. But in the *“cloud”* era, you can use whatever you think that may help you to bring useful features to your users better and sooner. And if you can, you must. That's why you started using Ruby. Don't ever forget it.

**FOURTH.** Polyglotism is here. There are several strategies to use different languages into the same application, like using a single platform like JVM, or communicating with messages through something like AMQP or ZeroMQ. You can push the *“the right tool for each job”* to a whole new level. So Rails may still be the best way to do web development, but web applications do much more than serving pages, and some of this other things might be better acomplished using Erlang, Haskell, Common Lisp, Clojure or whatever else you might find.

So, all I can say is: go out and play, break something if you must, and don't ever forget to have fun.

Thanks for reading this long!

*Feedback is more than welcome in [sgilperez@gmail.com](mailto:sgilperez@gmail.com) :)*

### Sources and References

* [My Job Went to India](http://pragprog.com/book/mjwti/my-job-went-to-india), by [Chad Fowler](http://chadfowler.com/) (the first edition is out of print and the second one has a different title: [The Passionate Programmer](http://pragprog.com/book/cfcar2/the-passionate-programmer))
* [Hackers & Painters](http://www.paulgraham.com/hackpaint.html), by [Paul Graham](http://www.paulgraham.com/)
* [Programming Erlang, Software for a Concurrent World](http://pragprog.com/book/jaerlang/programming-erlang), by [Joe Armstrong](http://armstrongonsoftware.blogspot.com/)
* [Real World Haskell](http://book.realworldhaskell.org/), by [Bryan O'Sullivan](http://www.serpentine.com/), [Don Stewart](http://donsbot.wordpress.com/), and [John Goerzen](http://www.complete.org/JohnGoerzen)
* [Practical Common Lisp](http://www.gigamonkeys.com/book/), by [Peter Siebel](http://www.gigamonkeys.com/)
* [Programming Clojure](http://pragprog.com/book/shcloj/programming-clojure), by [Stuart Halloway](http://thinkrelevance.com/team/members/stuart-halloway)
* [How To Create Your Own Freaking Awesome Programming Language](http://createyourproglang.com/), by [Marc-André Cournoyer](http://macournoyer.com/)
