# Syntax Alternatives

## LISPy
```
(fn add (a b)
    (+ a b))

(fn add2 (a)
    (add a 2))

(set x 4)

(add2 x)
```


## JSONish
```
// Verbose
{
    name: add
    args: [a, b],
    body: { 
        a + b 
    },
}

// Shorthand
{ add, [a, b], a+b }
```

## LISPy without parentheses
```
fn add (a b) (+ a b)
fn add2 (a) (add a 2)
set x 4
add2 x
```


# Thoughts

```
set y "5"
// "5"
set z *5
// 5
set z **5
// "5"
```

`*` Changes the form of something
Kinda like Deref, but not for dereferencing
It can only change to one thing
Form chains


### Quoting

```
(x + 5)
// (x + 5)
*(x + 5)
// `(+ x 5)`
*`(+ x 5)`
// "x + 5" 
```

That last one wouldn't actually behave that way
If you can't think of a better example, it's a bad idea

### Types
```
5:int
'int 5 // It's a normal function
```

any     dynamically typed
int     64-bit integer
float   64-bit floating poing
str     UTF-8 string
bool    true/false
()      Fixed-size list, (element) == element
[]      Variable-size list
{}      Code block
int?    nullable int


### Executing functions
```
add2 (n) (+ n 2)
// List of 3 elements
*add2 3
// 5
```

### Binding
Maybe everything is just a binding. Defining a function is binding with function definition syntax

```
set add2 (n) {
    n + 2
}

set x 1
// x = 1
add2 4
// 6

set add2 (n) { n + 2 }
```


### Pipelines
`|>` pipeline operator passes previous result as an argument
`|` execute statements in the middle of a pipeline
`|>` can continue after


```
fn add2 n { n + 2 }
fn mul3 n { n * 3 }
fn even n { n % 2 == 0 ? n ? nil }

[1, 2, 3, 4, 5]
|> add2
|> + 2
|> mul3
// [15, 18, 21, 24, 27]
|> even
// [18, 24]

{
    increment cursor
    |> until {
        *cursor == "\n"
    }
}

{
    increment cursor
    |> until { *cursor == "\n" }
    | return cursor
}

{
    range 1 5
    |> map { it * 3 }
    |> filter { it % 2 == 0 }
    | set offset 5
    |> map { it + offset }
    | set new-offset { it / 2 }
    |> map { it + new-offset }
    |> fold { acc + it }
}
```

### Text Editing
A text editing command is a target, action, and a number (default = 1)
```
edit line delete 3
edit cursor move-left
edit paragraph (replace "," ".")
```

Text editing targets have a next and previous value
```
edit (next cursor) == { increment cursor} 
edit (next line) == { increment cursor |> until { *cursor == "\n" } }
edit (next line) == { increment cursor |> until { cursor[0] == "\n" } }
edit (next "tacotime") == { increment cursor |> until { cursor[] == "tacotime" }}
```



### Nulls

`nil` value with opt-in nullable parameters.


```
fn full-name (first last?) { "${first} ${last ?? "jones"}" }

full-name "jim"
// "jim jones"

full-name "jim" "smith"
// "jim smith"

set last nil
full-name "jim" last
// "jim jones"


fn sum (a b) { a + b }

set x null
sum 3 x
// error, b is not nullable 

```

Works for typed variables too

```
set n?:int 5
set n?:int nil
set n:int nil // error
```