# Clojure Presentation
## Language Basics
### Everything is a Form
A form can be only one of two things: a value or an operation. Values look pretty similar to other values.
```clojure
1000
"strings in double quotes"
[ 1 2 3 ]
```
Note the whitespace delimited values in the vector. Nice right? Commas will be translated to whitespace and thus banished.

Forms are designated with parenthesis and only with parenthesis (well except for literal values as above). You won't see any if/fi case/esac nonesense, nor python's space delimeted blocks, or the soup of curly brackets, parenthesis, semicolons, commas etc. in many languages.
```clojure
(+ 1 2 3 4) // 10
(str "This " "is " "a clojure " "string") // "This is a clojure string"
(first [ 1 2 3 4 ]) // 1
(map inc [ 1 2 3 4 ]) // [ 2 3 4 5 ]
```
Parenthesis can be a little overwhelming. Here's a bit of code from the low-level html library ring:
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
However, it get's clearer with time. Compare this to other langauges like javascript that have a mix of expression designators:
```js
// JS
_.concat(
    _.map(
        [ 0, 1, 2],
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
        (fn [x] (+ x 1))
        [ 0 1 2 ])
    [ 4 5 ]
    [ 6 ])
```
Not a perfect example, and both can be rewritten using some better syntax (composition or chaining in js, thread macro in clojure). And hey, there's something new, an anonymous function. We'll get to that in a bit, but anonymous functions can, like es6 arrows, be written in a shorthand without the 'fn' operator. So, to be more fair to clojure, the above code could be written:
```clojure
(concat
    (map
        (#(+ % 1))
        [ 0 1 2 ])
    [ 4 5 ]
    [ 6 ])
```
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
// start with 4, divide by 2, multiply by 8, add 1, then add 1
```
