---
title: "Scala MultiMatch Pattern Matching"
tags: [PatternMatching multimatch]
---


##### Pattern Matching on Multiple case statements in Scala.
The original function and author can be found [here](http://stackoverflow.com/questions/6720205/idiomatic-way-to-convert-a-seqb). It is defined as:

~~~
implicit class MultiMatcher[A](a: A) {
  def multiMatch[B](pfs: PartialFunction[A, B]*): Seq[B] = {
    pfs.map(_.lift(a)).collect{ case Some(v) => v } 
  }
}
~~~

Coming from a Java background and as a newcomer to Scala the MultiMatch function seemed like magic for 2 reasons:

  - It allows us to use "pattern matching" like syntax to match multiple case statements (vs matching one case statement in normal pattern matching).
  - I wasn't familiar with the syntax and couldn't derive its functionality by looking at its definition.
	
It's also implemented in 5 lines of code!

#### Using it...
The stackoverflow ticket says it's an "Idiomatic way to convert A => Seq[B]", essentially getting a list of results for one input.  The example given is very succinct but my usage was slightly different and I wanted to try some things. My test example is:

~~~
object MultiMatcher {
  implicit class MultiMatcher[A](a: A) {
    def multiMatch[B](pfs: PartialFunction[A, B]*): Iterator[B] = {
      pfs.iterator.map(_.lift(a)).collect{ case Some(v) => v }
    }
  }

  val catSound:  Option[String] = Some("meow")
  val dogSound:  Option[String] = Some("woof")
  val dinoSound: Option[String] = None

  val case5: PartialFunction[Any, String ] = { case ( _, _, Some(dino: String)) => "5: " + dino }


  def main (args: Array[String]): Unit = {
    val mySeq: Iterator[String] = (catSound, dogSound, dinoSound).multiMatch(
      { case (Some(cat), Some(dog), Some(dino)) => "1: " + cat + "\t" + dog + "\t" +dino },
      { case (Some(cat), Some(dog),                 _) => "2: " + cat + "\t" + dog },
      { case (Some(cat),                _,                 _) => "3: " + cat },
      { case (              _, Some(dog),                _) => "4: " + "\t" + dog },
      case5)

    mySeq.foreach(println)
    // 2: meow woof
    // 3: meow
    // 4:           woof
  }
}
~~~


You can see I'm calling the MultiMatch function like a method (through implicit), on a tuple of 3 Options.  The input to the function is a handful of case statements.  This looks just like normal pattern matching, even more so if you don't use dot notation.  This tiny app prints out the sounds from the tuple for any cases matched and so we print out cases 2, 3, and 4. Good stuff!  After using it in some code I took a second look and still didn't understand how it actually did it.  Magic?!  I spent some time investigating and the main things that I didn't understand or had trouble with where:

- ##### Partial Functions
- ##### Asterisk character (or Repeated Parameters)
- ##### Partial Function Lifting
- ##### Applying the input to the function (vs applying the function to the input)
- ##### "collect" (on an Iterator)
- ##### Lazy Evaluation   
  
----

## Partial Functions

##### "PartialFunction\[A, B\]"
My confusion with partial functions wasn't just the new type that has two type parameters, but that it accepts case statements, huh? Well, a partial function is one that won't work (ie. is not defined) for all inputs, like "9/x" which is not defined when x = 0.  The actual PartialFunction type includes a method "isDefinedAt" to test its namesake.  That's all the type is; a function where you can test if the input is valid  
... and that is exactly what a case statement is; something which tests the input type!

----

## Asterisk character (or Repeated Parameters) 

##### "multiMatch\[B\](pfs: PartialFunction\[A, B\]*)"
This is dead simple; it states that we can use any number of arguments and the result is an Array with those parameters.  Made sense since we used MultiMatch with variable numbers of cases.

----

## Partial Function Lifting & Applying the input to the function

There are various uses of "lift" and their explanations can be found [here](http://stackoverflow.com/questions/17965059/what-is-lifting-in-scala).

Ours, is of course for use on PartialFunctions.  This was confusing because its context is:

~~~
pfs.iterator.map(_.lift(a)).collect{ case Some(v) => v }
~~~

... where with all my questions I wasn't initially sure what we where mapping on, and PartialFunction "lift" does not take a parameter.  So what's going on here with "lift(a)"?  First, we are surely calling "lift" on a PartialFunction as we are iterating over our Array of PartialFunctions and calling map on each one. "lift" will then return an Option[B] instead of B.  So we get Option(\<OUR_CASE_STATEMENT\>) something like:

~~~
Option({ case (Some(cat), Some(dog), Some(dino)) => "1: " + cat + "\t" + dog + "\t" +dino })
~~~

... and passing it argument "a".  In other words we are applying our function Option[B] to "a". That terminology is the same ("passing the argument 'a' to the function" and "applying the function to argument 'a'"), it's just coming from the perspective of the function or from the argument.  The Scala way is the latter and everything is an object including functions so we need a way to pass the argument to the object.  That's done through the "apply" method which every object has.  Because it's so ubiquitous, Scala made it so we don't need to type "apply" everywhere and the shorthand is to just use parenthesis.  Coming back to "lift(a)", we're really not passing "a" to "lift" but to its result and

~~~
lift(a)
~~~

can also be typed as

~~~
lift.apply(a)
~~~

...which means we're passing our animal sound Options tuple (argument) to our case statements (function).

On a slight tangent; this is how Scala bridges the gap between the Object-Oriented world and the Functional Programming world; with "apply"!



----

## "collect" (on an Iterator)  
This is also a simple concept, but I found it interesting.  It is essentially the "map" function but instead of having as its input a regular function, it takes a partial function.  That's exactly what we pass into it, a case statement:

~~~
.collect{ case Some(v) => v } 
~~~

----

## Lazy Evaluation
Being new, I initially didn't know that Iterators where lazily evaluated. I tried printing in the case statements but nothing happened even though "mySeq" was defined.  Iterators evaluate lazily because they could be very large (or infinite) and processing them at definition time is a bad idea.  So I changed that implementation to print through the items in "mySeq":

~~~
mySeq.foreach(println)
~~~

---

## Summary
Scala can be hard to read sometimes as someone new to it and the Functional Programming paradigm, but it's powerful stuff. Lastly, if you notice I parameterized the last case: "case5":

~~~
val case5: PartialFunction[Any, String ] = { case ( _, _, Some(dino: String)) => "5: " + dino }
~~~

I wanted to see if this could be done, but also my intent was to see how far I can go with this pattern. Using the asterisk we can use any number of case inputs.  Can we then parameterize them?  Of course PartialFunction is a type and we can create an object from them.  This means we can dynamically set the cases we want to use.  But can we dynamically create the cases?  Initially I tried working with the PartialFunction type and had a hard time with it, but there is another way with the Map Data Type!  As it turns out Scala Map type is a PartialFunction.  I'm not sure what the use case for this would actually be, but I found it interesting. 






