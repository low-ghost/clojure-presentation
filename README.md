# Clojure For Microservice Architectures
## Why Clojure
### Simplicity
Clojure focuses heavily on minimizing complexity. The language is built from the ground up with simplicity in mind and lisp variants have the shallowest learning curve of any programing language. Achieving simplicity without reducing power and flexibility is the most important goal of any language. Simplicity means faster iteration, faster developer onloading, easier and more effective debugging, eazier refactoring and less stomping on your keyboard.
### Speed
Clojure is impressively fast. As a hand-wavy summation, clojure is about 5 times faster than [python] (http://benchmarksgame.alioth.debian.org/u64q/compare.php?lang=clojure&lang2=python3), ruby and javascript and is [generally at the tail end of languages designed for speed](http://benchmarksgame.alioth.debian.org/u64q/which-programs-are-fastest.html), just a little behind java itself, scala and go but beating C#.
### Community
A strong community gives clojure excellent tooling, plenty of helpful tutorials, great books and videos and a good warm feeling that the language isn't going to just die in obscurity.
### Macros
A language with dead-simple macro support is ideal for microservices. With macros, a developer can write the language that best suites the purpose of the particular domain of a microservice. Think about it, a language that can dynamically write itself is intensly powerful and flexible. Other languages have some macro support, but lisp is unique in that you don't have to learn essentially a different language or parse an AST. It's almost as easy to write a macro as it is a function.
### Java Interop and the JVM
Java and clojure are much more closely bound than the word interoperability normally implies. Calling java code from clojure is incredibly simple and remains within the idiomatic bounds of clojure's syntax. Infact, some of the clojure language actually is java; strings, for example are just java.lang.String. Not to scare the reader with parenthesis right off the bat, but here's a few quick examples to show just how congruent the languages are:
```clojure
(.toUpperCase "this string")
; => "THIS STRING"
; is exactly equivalent to "this string".toUpperCase() in java
(Math/floor 8.123)
; => 8
; Uses java's java.lang.Math. All java.lang classes are imported by default
```
Being on the JVM means that a developer has access to anything within the vast java ecosystem. It also means that you can run clojure code in the exact same way and in the exact same environments as java. You can even (but why in hell would you) use maven. Finally, it means that developers could write their api's and web framework in simple, user-friendly clojure and drop to java when complex, cpu heavy computations are needed. You might never need to though, given clojure's speed.
### Functional Programming for Easy Concurrency and Immutable Data
Notice how I had to add the prepositional phrase to the above title just to stop you from squirming at the word 'Functional'? Clojure has plenty of OO constructs, but it is pretty heavily a functional language. This is good news for a microservice because it means incredibly easy parallelizaion while removing possible complications of race conditions and so on. Clojure also has data structures which are immutable by default (but which can be worked with in mutable ways if you really need to). This removes a slew of potential bugs and makes programs easier to reason about.
## Language Basics
### Everything is a Form
A form can be only one of two things: a value or an operation. Values look pretty similar to other values.
```clojure
1000
"strings in double quotes"
[1 2 3]
```
Note the whitespace delimited values in the vector. Nice right? Commas will be translated to whitespace and thus banished.

Forms are designated with parenthesis and only with parenthesis (well except for literal values as above). You won't see any if/fi case/esac nonesense, nor python's space delimeted blocks, or the soup of curly brackets, parenthesis, semicolons, commas etc. in many languages.
```clojure
(+ 1 2 3 4) ; => 10
(str "This " "is " "a clojure " "string") ; => "This is a clojure string"
(first [1 2 3 4]) ; => 1
(map inc [1 2 3 4]) ; => [2 3 4 5]
```
These forms are the basis of all clojure. Even the data types are actually just shorthand for their form constructor:
```
(vector 1 2 3)
(list 4 5 6)
```
But more on that later.
Parenthesis can be a little overwhelming. Here's a bit of what is actually perfectly neat and reasonable code from the low-level html library [ring](https://github.com/ring-clojure/ring):
```clojure
(defn url-response
  "Return a response for the supplied URL."
  {:added "1.2"}
  [^URL url]
  (if-let [data (resource-data url)]
    (-> (response (:content data))
        (content-length (:content-length data))
        (last-modified (:last-modified data)))))
```
However, it get's clearer with time. Compare this to other langauges like javascript that have a mix of expression designators: curly braces, parentheses, dot and infix operators, commas, semi-colons etc:
```js
// JS
_.concat(
    _.map(
        [ 0, 1, 2 ],
        (x) => {
    
            return x + 1;
    
        }
    ),
    [ 4, 5 ],
    [ 6 ]
);
```
```clojure
(concat
    (map
        (#(+ % 1))
        [0 1 2])
    [4 5] [6])
```
Not a perfect example, and both can be rewritten using some better syntax (composition or chaining in js, thread macro in clojure). And hey, there's something new, an anonymous function. We'll get to that in a bit.
Parenthesis and the form structure can also be confusing where traditional infix operation is used, mathematics in particular. For instance, the follow expression
```code
1 + 4 / 2 * 8 + 1
```
Makes sense to us because of our elementary school math classes. To do the same in clojure would look like
```clojure
(+ (* (/ 4 2) 8) 1 1)
```
Weird, right? But lets break down the standard math equation. We know order of operations, but that expression might be clearer with some of the parenthesis in place and reordered:
```code
((4 / 2) * 8) + 1 + 1
```
Little bit closer to clojure. All of the expressions so far translate into english as 'divide 4 by 2 then multiply by 8 then add 1 then add 1'. If we use the 'then' type operator in clojure called the thread first macro, this code looks even more like our english:
```clojure
(-> 4 (/ 2) (* 8) (+ 1 1))
; start with 4, divide by 2, multiply by 8, add 1, then add 1
```
### Values
We've already seen numbers, strings and vectors: `1 "sting" [ 1 2 3 ]`. We'll jump into these and others in a bit more detail.
##### Numbers
Clojure numbers are from java.lang.Number and support BigInteger, BigDecimal, plus clojure's own Ratio. A ratio looks like `1/2` and does operations like:
```clojure
(+ 1/2 5/7) ; => 17/14
```
BigIntegers and BigDecimals are contagious such that operations with them convert lesser types. Clojure also provides apostrophied methods--+', -', etc.--that convert types automatically.
##### Strings
Strings are java strings. More later, but defined with double quotes.
```clojure
"a string"
```
##### Vectors
Vectors are defined with square brackets and can contain any values. E.g.
```clojure
[1, 1/2, "one"]
```
Vectors are array-backed, numerically indexed and equate via length first, then each value.
###### Lists
Lists are defined with a single quote and parentheses like:
```clojure
'(1, 1/2, "one")
```
###### Maps
Maps are defined with curly brackets:
```clojure
{"key" "value" "nextKey" "nextValue"}
```
However, using keywords in maps is more performant. Keywords look like:
```clojure
{:key "value" :nextKey "nextValue"}
```
Duplicate keys will cause an exception. However, this'll be ok, though potentially confusing and to be avoided:
```clojure
{:a 1 "a" 2}
```
###### Sets
Sets are defined with hash and square brackets.
```clojure
#{1 2 3}
```
Duplicate keys will throw.
### Working with collections
One of the tenants of clojure is programming to abstraction such that functions work similarly regardless of the type of the paramater. Thus thinking in terms of datatypes and which functions can work on them is probably wrong headed and it would be better to think 'cons adds an element at the beginning of that collection' etc. However, it'll help get our feet wet with the data types we've covered.
##### Getting Values
You can get values out of maps, sets, vectors etc via get or by using the value as a getter:
```clojure
(get {:key "value" :nextKey "nextValue"} :key) ; => "value"
({:key "value" :nextKey "nextValue"} :key) ; => "value"
({"key" "value" "nextKey" "nextValue"} "key") ; => "value"
(get [ 1 2 3 ] 0) ; => 1
([ 1 2 3] 1) ; => 2
```
Some other neat functions and functionality: get can provide a default and get-in can get nested values:
```clojure
(get {:key "value"} :nextKey "not in map") ; => "not in map"
({:key "value"} :nextKey "not in map") ; => "not in map"
(get-in {:key {:nested {:thrice "value"}}} [:key :nested :thrice]) ; => "value"
```
##### Adding and removing values
Adding elements:
```clojure
// assoc for vectors and maps
(assoc [1 2 3] 2 4) ; => [1 2 4]
(assoc [1 2 3] 3 4) ; => [1 2 3 4]
(assoc {:a 1 :b 2} :c 3 :d 4) ; => {:a 1 :b 2 :c 3 :d 4}
// cons for sets, vectors and lists
(cons 1 #{2 3 4}) ; => [1 2 3 4]
(cons 1 [2 3 4]) ; => [1 2 3 4]
(cons 1 '(2 3 4)) ; => (4 1 2 3)
// conj for sets, vectors and lists
(conj #{1 2 3} 4 5) ; => #{1 2 3 4 5}
(conj [1 2 3] 4 5) ; => [1 2 3 4 5]
(conj '(1 2 3) 4 5) ; => (5 4 1 2 3)
// concat for sets, vectors, lists and sequences, at once
(concat '(1 2) [3 4] #{5 6} (range 7 9)) // (1 2 3 4 5 6 7 8) which is a lazy sequence
```
Removing elements:
```clojure
// dissoc for vectors and maps
(dissoc {:a 1 :b 2 :c 3} :b :c) s; => {:a 1}
// disj for sets
(disj #{10 20 30} 20 30 50) ; => #{10}
// pop for vectors and lists
(pop [1 2 3 4]) ; => [1 2 3]
(pop '(1 2 3 4)) ; => [1 2 3]
```
### Defining values and functions
Values are defined with the def function:
```clojure
(def greek-gods ["Zeus" "Apollo" "Ares"])
greek-gods ; => ["Zeus" "Apollo" "Ares"]
(def greek-goddesses #{"Hera" "Athena" "Demeter"})
greek-goddesses ; => #{"Hera" "Athena" "Demeter"}
```
Functions are defined with defn and uses square brackets to define paramaters and can have python docstring style comments:
```clojure
(defn multiply-by-5
  "Just multiplies the input by 5"
  [some-number]
  (* 5 some-number))
```
Functions in clojure can have different bodies based on their arity.
```clojure
(defn arity-arity
  "Does different things depending on the number of params"
  ([] (str "no args"))
  ([x] (str "One arg:" x))
  ([x y] (str "Two args:" x y)))
```
