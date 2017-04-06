A Python data scientist tries out Julia
==========================
Installation is a breeze. Just download the .dmg, and open it anywhere you want. 

Like Python, it's dynamically typed. So when you can assign an integer to a variable, and the variable will correctly self-identify itself as an integer. 
```
julia> a = 19
19

julia> typeof(a)
Int64
```
The REPL is the same. Strings must be inside double quotes. Single characters are not strings; they're Chars.
```
julia> b = "X"
"X"

julia> typeof(b)
ASCIIString

julia> c = 'X'
'X'

julia> typeof(c)
Char
```
Oddly, you're allowed to use special characters in variable names:
```
julia> ö = "a special character";

julia> typeof(ö)
ASCIIString
```
Comments:
```
julia> #= Long comments
       that span several lines
       go like this =#

julia> # single-line comments are like this.
```
A neat feature that data scientists might find useful is the ability to print out in color:
```
julia> print("Here's some regular text."); print_with_color(:red, " Here's a warning statement in red!"); print_with_color(:green, " Task is finished.");
Here's some regular text. Here's a warning statement in red! Task is finished.
```

You can declare numbers with `_` separators, so the human eye can quickly recognize the scale of large numbers, like so:
```
julia> a = 1_000_000
1000000
```

Types
------
"For utmost performance, you need to write type-stable code. Code is type-stable if the type of every variable does not vary over time" (page 38, Getting Started With Julie Programming).

"Adding type annotations to variables updated in the inner loop of a critical region of code can lead to drastic improvements in the performance by helping theJIT compiler remove some type checking." [Here's an article](http://www.johnmyleswhite.com/notebook/2013/12/06/writing-type-stable-code-in-julia/) with an example.

