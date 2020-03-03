# Langage de prog immaginaire lol

Core concepts:

- prefer symbols (operators) over methods or keywords for common operations (one example is `==>`, the equivalent of many languages' `return`), and methods over operators for rarely-used operations (examples are the modulo, or bitwise operations)
- include everything that you would generally use a library for (a perfect example is momentjs/date-fns and javascript)
- try to make most operations possible for a type, whilst staying logical (example: the division "/" is an alias to "+" for `path` (path) types, because concatenating paths with "/" feels natural, but you can't do a logical "not" on paths)
- be type safe and type flexible
- declare defaults, types and as much things as possible in a function declaration
- every method returns something, and everything is a method.
- be as clean as possible
    - identation-based
    - one statement per line
    - as little punctuation as possible (eg. function calls don't require parentheses, as *everything is callable*)
    - one-letter variables reserved for automatic variables (eg. `v`, `k` and `i` inside `iterate` loops)
    - keywords are readable but everything is done so that you can assign every word you like (eg. type names only matter inside `as` or `~` operations), because *every keyword is in fact a method of the `core` module, and writing 
    ```
    if 4/3
        do_thing 8
    ```
    is syntactic sugar for writing `core.if 4/3, { do_thing 8 }`
    - `{}` *always* refers to the creation of a *scope*. A *scope* simply defines multiple statements: *functions are in fact scopes that have been stored in a variable—the function's name.*
- provide as much types as possible, but do not make duplicates (unlike python's tuples, lists and sets, that represent basically the same thing, with little-to-no differences):
    - colors, regular expressions and others require their own type because they represent different things: a color should not be represented by a string because it is *not* a sentence, but a completely different type of data.
    - all iterables can be merged into a single type: a map. A list or array is simply a map with numerical, incrementing keys. An iterable also as an implicit order, where—in the special case of an array—indexes (holding the order of a map's items) and keys (identifying what the value represents) are the same thing.
    - functions *are* different. We can't just say that it's a type like another one. That's because functions or methods represent a *protocol*, a sequence of actions and conditions, not pure and atomic *data*. But functions are held by variables. So, as in python, functions can be passed to another function to execute them (eg. to implement callbacks)
- writing programs should feel natural. That's why the language prefers keywords over symbols when it is unusual (eg. `and`, `or` and `not` instead of `&&`, `||` and `!`)

## TO CATEGORIZE
```
a.b c == b c of a
example: 7..10.every 9 == every 9 of 7..10
```

## Comments

```kt
// Single-line

/* Multi
Line
Comment.
*/
```

## Operators

Space around operators is _required_, except for `-`.

Operator | General behavior (see [types](#Types) for specific behaviors by type) | Example(s)
:-------:|---------------------------------------|-------------------
`+`      | Addition | `8+9 ==> 17`
`-`      | Subtraction | `9-10 ==> -1`<br>`-8 == -1*8 ==> yes`
`*`      | Multiplication
`/`      | Subtraction
`**`     | Exponentiation
`%`      | _nothing_, as the methods `.even?`, `.odd?`, `.every n` and `.modulo n` will suffice.
`§`      | Format strings | `"{a:.05f}" § [a: math.pi] ==> 3.14159`
`$`      | Interpolation | `"I am $name" ==> "I am Groot"` (if `name` is defined and equal to `"Groot"`)
`==`     | Equality
`===`    | Strict equality
`~=`     | Approximative equality
`>`      | Strict superiority
`<`      | Strict inferiority
`>=`     | Superiority
`<=`     | Inferiority
`<<`     | Appending
`>>`     | Prepending
`++`     | Incrementation
`--`     | Decrementation
`><`     | Squashing (_fitting a number between 0 and 1 to a range_) | `math.e >< 4..8 ==> `
`..`     | Range (inclusive)
`...`    | Splat operator
`in` or `∈` | Ownership | `8 in [1,5,8,9] ==> yes`
`or` or `⋁` | Logical or
`and` or `⋀` | Logical and
`not` or `!` | Negation
`=`    | Assignement
`->`   | Direct assignement (see [the `->` operator](Opérateur->))
`-->`  | Direct assignement from argument (see [the `-->` operator](Operator-->)) | `a = b(a) == a-->b ==> yes`
`==>`  | Value returning
`.` | Member access, decimal separator
`:` | Key-value mapping
`{` | Scope start
`}` | Scope end
`(` ... `)` | Grouping
`[` ... `]` | Iterable creation
`;` | ~~Statement ending~~ this is a beautiful language. You don't need any semicolon anywhere.
`,` | Enumeration
`<<` ... `>>` or `〈` ... `〉` | Typed value declaration
`|` | Separation of flags and arguments
`@` | Prototype acess | `@string.foo = fun (a) ==> a+a*value`<br>`"lolz".foo 2 ==> "2lolzlolz"`
`&` | Variable reference access (_used to modify a variable from an outer scope_)
`~` | Equality of types | `"abc" ~ string ==> yes`

## Scopes
New scopes are created *only* when a specific piece of code is encapsulated in `{}`'s, or in a method/function definition

Please encapsulate if/else/switch/when/... only when *absolutely necessary*

## Defining functions

### Signature

`fun function_name = (arguments | flags) { ... }`

### Arguments

- *Required* `arg`
- *Typed* `arg:type`
- *With default* `arg=default_value`
- *Optional* `arg?`<br>Same as `arg=nothing`
- *Optional typed* `arg:string?`<br>Defaults to the type's [initialization value](#types)
- *Other arguments* `...options`—Registers `options` as a `[name:value]` map containing args whose name don't match arguments in signature

### Flags
- *Defaults to `yes`* : `!flag_name`
- *Defaults to `no`* : `flag_name`

### Example

NOTE: this is the recommended function declaration code style when the combined count of arguments and flags is greater than 4 (`...` operator counts as an argument)
```kt
use stdout from io
use term

colored_output = (
    message:string, end="\n", color:color?, prefixes...
    | monochrome, !use_ansi_pkg, other_flags...
) ==> {
    start = prefixes.values as string
    // Prefer to "$start$message$end"—only use it when the string is not only variables
    message = start + message + end
    if monochrome
        warn "Use `output` instead" cause: deprecation
        ==> output message

    if color not in term::ansi_codes.keys
        warn "$color is not a valid ANSI color name." fallback: "monochrome output"
        ==> output message
    
    stdout ansi_colors.$color // Prefer this to []-Notation. Reserve it for electing ranges

}

fun output = (string string) ==> stdout string
```

## Calling functions
### Omitting parenthesis
If you call a function with arguments or flags, you can only ommit parenthesis if you aren't chaining functions:

```kt
func1.func2.func3 //Resolves as func1().func2().func3()
func1 param //Resolves as func1(param)
func1 param.func2 //Resolves as func1(param.func2())
func1 func2 // Resolves as func1(func2())
```
### Named parameter shortcut
If you have a variable whose name is the same as a function's argument, 
you can call it without using some stupid `long_argument_name: long_argument_name`: Just use `$long_argument_name`
### Flags-only shortcut
If you want to call a function but only need to activate/deactivate a flag, you simply do `function_name|flag1, flag2`
```kt
fun func = (a:number?, b:any=0.1 | flag) ==> log(a, b, flag)
param = "lol"
a = 2
b = 3

/* NOTE: the function actually RETURNS the string and outputs it.
   We only show ouput as comments for clarity's sake.
*/
func param                //TypeError: "lol" is not a number
func(a)                   //a = number 2, b = number 0.1, flag = no
func(a, "string")         //a = number 2, b = string string, flag = no
func a, b | flag          //a = number 2, b = number 3, flag = yon yes
func a: 66.6 b: "thingie" //a = number 66.6, b = number 3, 
func(a, b | flag)         //a = number 2, b = number 3, flag = yon yes
func|flag                 //a = number 0, b = number 0.1, flag = yon yes
func $b                   //a = number 0, b = number 3, flag = yon no
```
## Assignements de variables

### Normal

```kt

thing = "thinixkdk" //==> string tjifbfjdn
string thing = "tjifbfjdn" //==> string "tjifbfjdn"
thing = 5 //=> Error: can't set the string "thing" to a number
```

### `Dynamic`

```kt
dynamic thing = "jdjekskk" //==> string "jdjekskk"
thing = 5 //==> number 5
```

### `volatile`
Supprime la variable après un bloc *d'indentation*
```kt
use stdout from io
my_str = "blablabla"
{
    volatile my_str = "bla"
    log my_str //==> string~bla string blablabla
    stdout my_str //bla
}
stdout my_str //blablabla

{
    volatile other = 3.1415
    log other  //==> string~other
}
stdout other.to_str //UndefinedError: variable "other" is not defined here. Check for a potential "volatile" keyword
```

### Opérateur `->`

    thing = thing.uppercase
    <==>
    thing->uppercase

Ducoup TOUTES les fonctions/méthodes retournent des valeurs par défaut, dans les classes, self est return par la methode au lieu de muter self

## Types

### Opérateur `as`
Coerce un type vers un autre:

```kt
// In the real world, use string.alphabet
my_str = "abcdefghijklmnopqrstuvwxyz"
array[number] my_arr = my
```

### Opérateur `~` ou `is`
Retourne `yes` si `my_var` est du type `type`:
```
my_var = 2.51
my_var ~ number //==> yes
my_var is a string //==> no
```

NOTE: Vous *pouvez* utiliser les noms de types comme variables, parce que `number`, `string`, ... représente un type seulement...
- lors d'un assignement typé (`path my_pth = ...`)
- lors d'une déclaration de valeur typée (`〈pth ...〉`)
- dans le contexte de l'opérateur `as`
- dans le contexte de l'opérateur `is`/`~`

### **date**es
```kt
my_dat = 2019-08-14T08:07 or 2019-08-14 or 08:06
//==> date 2019-08-14T08:07
```

Initialization: `date.now`<br>
Format: `[<year>-<month>-<day>]T[<hours>:<minutes>]`

### **number**bers (int==float)

```kt
my_num = 1000 or 0.458 or 10_000_000
```
Initialization: `0`<br>
Format: `[<digit-or-underscore...>.]<digit-or-underscore...>`

### **R**a**ng**es

### **string**ings

```kt
my_str = "sample text" or `sample_text` // both are the same
my_str = 'sample_text' //SyntaxError
```
Initialization: `""`<br>
Format: `"<string>"`

### **R**e**g**ular e**x**pressions

```kt
my_rgx = ^[0-9\n]?.+(\s*)$ //==> RGX ^[0-9\n]?.+(\s*)$
my_rgx = [\w_-]{3} //error cause: syntax, message: Substraction has no right-side operand (tried to substract "w_")
my_rgx = ^$ // RGX ^$
my_rgx = ^^\d+.?$$ //error cause: syntax, message: Cannot parse regular expression, failed at char #2: ^
```
#### Initialization
`^$`

#### Format
`^<regular expression>$`

#### Operators
Operator | Action for this type      | Commutative?
:-------:|---------------------------|---------------
`a + b`  | Appends `a` to `b`  (without bounds (^ and $))      | No
`a - b`  | Removes `a` from `b`  (without bounds (^ and $))    | No
`a / b`  | *raises an error*         | 
`a * b`  | Repeats `a` `b` times (without bounds (^ and $))| No
`a == b` | Checks if `a` matches the pattern `b` | No
`a or b` | Returns `"(?:$a)|(?:$b)" as rgx` (without bounds (^ and $))| Yes
`a and b`| Returns `"(?:(?:$a)(?:$b))|(?:(?:$b)(?:$a))" as rgx` (without bounds (^ and $))| Yes
`a ~= b` | Same as `==` | No
`a in b` | Same as `==` | No
`a === b`| Checks if the two patterns are equivalent

#### Conversion to other types
_Types not specified here cannot be converted, and will raise an error with cause `type` upon a conversion attempt._
Target type | Result | Example | Automatic/implicit conversion?
:----------:|--------|--------- | ---
`string`       | The pattern's representation, as typed | `^[\w-_]+$ as string ==> "^[\w-_]+$"` | No

#### Methods

Method signature  | Returns | Example(s)
:-----------------|:--------|:-----
`.without_bounds` | `rgx`   | `^(\d+)$.without_bounds == "(\d+)" as rgx`
`.try string:string`    | `yon`   | `^\w{4}$.try "qwer" == true`<br>`^\w{4}$.try "78azce" == false`
`.capture string:string|allow_no_matches`| `map`   | `〈rgx (?p<foo>[a-z0-9-])\+(\d+)〉.capture "8+74" == [0:"8+74", foo:"8", 2:"74"]`<br>`〈rgx ([a-z0-9-])\+(\d+)〉.capture "8" //Error cause: No groups found while searching for pattern "([a-z0-9-])\+(\d+)" in string "8"`<br>`〈rgx ([a-z0-9-])\+(\d+)〉.capture|allow_no_matches "8" == []`
### **P**a**th**s

```kt
my_pth = /home/lol/machin //==> path /home/lol/machin
my_pth = ~/new/thingie //==> path ~/new/thingie
```
#### Initialization
`path.cwd or path.home` (Current working directory of terminal, or fallback to home directory)

#### Format
`<"~" || "/"><identifier-friendly-or-string>/[<identifier-friendly-or-string>/[<identifier-friendly-or-string>/...]]`

#### Operators
Operator | Action for this type      | Commutative?
:-------:|---------------------------|---------------
`a + b`  | Appends `a` to `b`        | No
`a - b`  | Removes `a` from `b`      | No
`a / b`  | Same as `+`               | No
`a * b`  | Repeats path `a` `b` times| No
`a % b`  | *raises an error*         | 
`a == b` | Checks if the paths points to the same location (does not take symlinks into account) | Yes
`a ~= b` | Checks if the paths points to the same location (takes symlinks into account) | Yes
`a === b`| Same as `a as string == b as string` | Yes
`a in b` | Checks if the file or folder `a` exists at the location `b` | No

### **array**ays

```kt
my_arr = ["kfid", 9394, [ 8,88,8]]
array[string] my_arr = ["kdkd" 666] //TypeError: my_arr is an array of strings, can't add number 666 to it.
arr2 = [] //==> array (empty)
arr2->to_y //==> YON no
```
#### Initialization
`[]`

#### Format
`[<open>][<value>[, <value>[, <value> ...]]][<close>]
`(`<close> = "]", <open> = "["`)

#### Operators
Operator | Action for this type         | Commutative?
:-------:|------------------------------|---------------
`a + b`  | Merges `a` and `b`           | Yes
`a - b`  | Removes `b`'s items from `a` | No
`a / b`  | *raises an error*            | No
`a * b`  | Copies `a`'s items `b` times in itself | No


### **Map**s

```kt
my_map = key: 8383, "key-2": "jdkdk" //==> MAP key: 8383, key-2: "jdkdk"
my_map.key //==> number 8383
my_map.key-2 //==> number 8381
my_map."key-2" //==> string jdkdk
my_map->key //==> number 8383
my_map //==> number 8383

map2 = [:] //==> MAP{}
map2->to_y //==> YON no

item = "test"
map3 = [
    :item, // Same as item: item
    other: 5
] //==> map item: "test", other: 5
```
Initialization: `[:]` <br>
Format: `[<open>][<key>: <value>[, <key>: <value>[, <key>: <value> ...]]][<close>]`<br>
(`<close> = "]", <open> = "["`)
### Booleans—**y**es-**o**r-**n**o's

```kt
my_yon = yes //==> YON yes

my_map = key: 8383, "key-2": "jdkdk" //==> MAP key: 8383, otherk/ey: "jdkdk"
my_map.keys.contains? "key2" //==> YON no
```
#### Initialization
`no`
#### Format
`<"yes" || "no">`
#### Operators
Operator | Action for this type      | Commutative?
:-------:|---------------------------|---------------
`a + b`  | Equivalent to `and`       | Yes
`a - b`  | *raises an error*         |
`a / b`  | *raises an error*         |
`a * b`  | Equivalent to `or`        | Yes
`a % b`  | *raises an error*         | 
`-a`     | Equivalent to `not`       |

### `nothing`

Représente une valeur nulle.

```kt
my_nothing = nothing //==> NOTHING
log("b" if my_nothing else "a") //b
my_nothing is nothing //==> YON yes
```

#### Initialization
`nothing`

#### Format
`nothing`

### **color**ors
```kt
my_col = #268CCE0A // RGBA
my_col = #268cce
my_col = blue //Undefined: Variable `blue` is not defined
```

#### Initialization
`#00000000` (`#00000000.to_rgba_str //==> rgba(0,0,0,0)`)
#### Format
`#<hexadecimal-digit * 6>`
#### Operators
Operator | Action for this type      | Commutative?
:-------:|---------------------------|---------------
`a + b`  | Mixes `a` and `b`         | Yes
`a - b`  | *TODO*                    | No
`a / b`  | Same as `+`               | No
`a * b`  | *raises an error*         | No
`a % b`  | *raises an error*         | 

## Fonctions anonymes

Quand une méthode/fonction accepte un argument de type `FUN` (function), il suffit de passer un bloc comme argument, et celui ci sera executé. La valeur de retour est automatique

```kt
map =
    id: 4, // comma not necessary when line breaks
    key: "tess"
//==> MAP id: 4, key: "tess"

array[MAP[string]] map2 = [
    [key: "ew", color: "#363637"],
    [key: "en", color: "#000000"],
    [key: "al", color: "#00FF00"]
]

map2->to_arr { this.key if != "al" } // same as this.key if this.key != "al"
//==> array "ew", "en"



map2 //==> array "ew", "en"
```

Exemple

```kt
class map
    //...

    def self.to_arr(FUN mapper) // fun = function
         array array = []
         each self
             //key & value defined automatically by `each`
             array += mapper self
        ==> array // return == "==>"
```

## Classes

mot clé "new" pour instancier.

les args passés à new sont automatiquement mis comme props de l'instance, si non utilisé par la méthode new

ex:

```kt
class MY_CLASS
    def new(a, b, c, d=5)
        self.d = nothing
        self.a = b unless b == 3

new MY_CLASS(1,4,6)
//==> MY_CLASS a: 4, c: 6
new MY_CLASS(1,3,8)
//==> MY_CLASS c: 8
```

## Exemple de code

TIP: Fait partie de la `stdlib`

```kt
use * from io // Imports every `io` symbol, but pollutes the namespace. `use io` would not (it'd prefix them with io::)
use term

class file
    fun new = (path path){}
        /*
        In the `new` method, arguments are automatically saved as instance properties.
        To prevent this, delete the variable at the end of the method, as such:
        ```kt
        def new(preserved_arg, deletethis)
            // Do stuff with `deletethis`...
            delete deletethis
        ```
        */

    fun self.contents = () ==> read path //Will search for `path` then `self.path`

    fun self.valid_encoding? = {
        encoding->file.encoding // equivalent to self.encoding = file.encoding
        with encoding
            when "utf8" or "utf-8"
                encoding->"utf8"
                ==> yes
            when "ascii"
                ==> yes
            else
                ==> no
    }

    fun self.log(color?) // Équivalent à "color=nothing"
        if "utf8" and not term::supports? "utf8"
            volatile term = term::name
            warn "UTF-8 not supported on $term. The output might be corrupted."
            stdout contents //from package "io"
        else if color is nothing
            log contents
        else
            log(contents, =color)  // Équivalent à "color=color"

```

## Throwing errors, debugging

3 keywords: `log`, `warn` and `fail`

- `log` prints the value with debug info (type,...) while setting `io::stdout` only outputs the string as-is (and can't output something else than a string)
- `warn` prints a warning message but allows further execution
- `fail` stops execution and outputs a message.

## Explicitly typed values
Au lieu d'utiliser des syntaxes spéciales pour les types on peut faire:

\<\<TYPE CONTENT\>\> ou 〈TYPE CONTENT〉

### Exemples
```v
〈rgx [\d_-]+ ?〉 //==> rgx [\d_-]+ ?
〈map thing: stuff, other thing: other stuff〉 //==> map thing: stuff, other thing: other stuff
〈str mon contenu sans guillemets
avec des retours à la ligne littéraux et des $dollars〉 //string {mon contenu sans guillemets\\navec des retours à la ligne littéraux et des $dollars}

〈pth mon/chemin/〉 //==> path mon/chemin
```

## Operator shorcuts `+=`, `++`, `-=`, `--`, `/=`, `%=`, `*=`, `?=` and `??=`

- `+=`, `++`, `-=`, `--`, `/=`, `%=` and `*=` behave like you would expect:
```js
my_var += my_val
//is the same as
my_var = my_var + my_val

my_var++
//is the same as
my_var+=1
//which is the same as
my_var = my_var + 1
```

- `??=` assigns a value if the variable is `nothing`:
```js
my_var ??= my_val
// is the same as
my_var = my_val if is nothing
```
- `?=` assings a value if the variable evaluates to `no` or is `nothing`
```js
my_var ?= my_val
// is the same as
my_var = my_val if yes
```
