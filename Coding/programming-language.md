
# Langage de prog immaginaire lol

Ne requiert PAS d'utilisation de lib externe type momentjs, lodash, etc: tout est inclus

## Comments

```kt
// Single-line

/* Multi
Line
Comment.
*/
```

## Control blocks
### As modifiers
As in ruby, the following keywords can be used after a statement, as a _modifier_.
- For conditions, the statement modified will be executed if the condition is validated
- For loops, the statement will be considered as the loop's body (for the do...until/do...while blocks, the `do` must be placed before the statement, as with its classic "block" usage)
### Conditions: `if`, `unless`, `when`
`if` works as expected, with no parenthesis around the condition
`unless condition` is the same as `if not (condition)`

`when` is a `switch`-like construct, that returns the result of what is assigned to the first condition met.
When no `else` case is provided, it falls back to `nothing`

The condition will be built using the part after the `when` and before the `:`
See the comments below
```kt
age_category = 
	when age
		== 69: "Nice" //Computed condition: age == 69
		in 1..3: "Toddler" //Computed condition: age in 1..3
		in 3..12: "Kid"
		in 12..17: "Teenager"
		in 17..100: "Adult"
		> 100: "World-record holder"
		else: "[insert thanos meme] Impossible." 
```
### Loops: `while`, `with-while-do` `until`, `do...while`, `do...until`, `iterate`, `break`, `next`
`while`, `do...while` and `break` work as expected.
`next` is like `continue`

`until condition` is the same as `while not (condition)`
The `while` loop can be a standard definition-condition-assignement shortcut, like a regular `for` loop:
```
with variable=value while condition do assignement
//Example
with i=0 while i<=arr.length do i+=1
```
For this particular example, though, the `iterate` loop is way more appropriate and recommended:

`iterate` is used with an array or a map, and loops through it, setting `key`, `index` and `value` variables.
You can also define your own variables that will be computed using  `key`, `index` and/or `value` at each iteration. If you override one of these methods, the previous assignement will not affect the next.
You can also use the `if` or `unless` modifiers here.
The syntax is:
```
iterate <iterable> [with <variable>=<expresion>[, <variable>=<expresion>[, <variable>=<expresion>[, ...]]]] [<"if" || "unless"> <condition>]
```

```kt
map[num or yon or str] my_map = [
    thing: yes
    amismart: no
    thingie: 4
    stuff: "yikes"
]
iterate my_map with nth=index+1.ordinal|short if value
    ouput "$nth $key"
//1st thing
//3rd thingie
//4th stuff

//Could be used to iterate over half the items:
iterate my_map if index.odd
    output index
```
### Others: `with`
`with` is a handler that automatically executes stuff before and/or after the `with`, using the `with`'s variable (when applicable) and value passed as arguments


## Scopes
New scopes are created *only* when a specific piece of code is encapsulated in `{}`'s

Please encapsulate if/else/switch/when/... only when *absolutely necessary*
Using curly braces while defining methods/functions is recommended (unless writing short functions)

## Defining functions

### Signature

`fun function_name = (arguments | flags) { ... }`
#### Anonymous functions
**with arguments**
`(arg1, arg2) ==> arg1+arg2*2` 
**without arguments**
`fun ==> "yikes"`

### Arguments

- *Required* `arg`
- *Typed*
    - *Hard type* `type arg`<br>
    Fails with cause `Type` upon an attempt to pass a value of a different type

    - *Soft type* `type:arg`<br>
    Warns with cause `Type` upon non-trivial conversions (conversions between str, num & yons are considered trivial.)
    - *Multi-types* `str or arr[num] arg`<br>
    At this point, putting multiple arguments on the same line on the function definition is a _really_ bad idea, readability-wise
    - *Soft multi-types* `str or arr as num:arg`<br>
    Will accept `num`s and convert silently `str`s and `arr`s to `num`s
- *With default* `arg=default_value`
- *Optional* `arg?`<br>
  Same as `arg=nothing`

- *Optional typed* 
    - *Hard type* `str arg?`
    - *Soft type* `str:arg?`<br>Defaults to the type's [initialization value](#types)
- *Other arguments* `...options`—Registers `options` as a `[name:value]` map containing args whose name don't match arguments in signature

### Flags
- *Defaults to `yes`* : `!flag_name`
- *Defaults to `no`* : `flag_name`

### Example

NOTE: this is the recommended function declaration code style when the combined count of arguments and flags is greater than 4 (`...` operator counts as an argument):
- one argument per line
- flags on the same line
```kt
use stdout from io
use term

fun colored_output(
    str:message // no need for commas when the line breaks
    end="\n"
    str or col:color? // same as str or col as col:color?
    ...prefixes
    | monochrome, !use_ansi_pkg
) = {

    start = prefixes.values as str
    // Prefer to "$start$message$end"—only use it when the string is not only variables
    message = concat (start, message, end)
    if monochrome
        warn "Use `output` instead" cause: deprecation
        ==> output message

    if color not in term::ansi_codes.keys
        warn "$color is not a valid ANSI color name." fallback: "monochrome output"
        ==> output message
    
    stdout ansi_colors.$color // Prefer this to []-Notation. Reserve it for lists & selecting ranges

}

fun output = (STR string) ==> stdout string
```

## Calling functions
```kt
fun func = (a:num?, b:any=0.1 | flag) ==> debug(a, b, flag)
param = "lol"
a = 2
b = 3

/* NOTE: the function actually RETURNS the string and outputs it.
   We only show ouput as comments for clarity's sake.
*/
func param                //TypeError: "lol" is not a number
func(a)                   //a = num 2, b = num 0.1, flag = no
func(a, "string")         //a = num 2, b = str string, flag = no
func a, b | flag          //a = num 2, b = num 3, flag = yon yes
func a: 66.6 b: "thingie" //a = num 66.6, b = num 3, 
func(a, b | !flag)        //a = num 2, b = num 3, flag = yon no
func|flag                 //a = num 0, b = num 0.1, flag = yon yes
```
As you can see, parenthesis can sometimes be omitted, that's why **properties and methods cannot use the same identifiers.** Internally, doing `thing.stuff` will call `.stuff.get()`, even if it's a property. Properties have implicitly defined methods that do this:
```kt
//Example class
class Foo 
    def new(a, b)
        this.a = a.kebabcase
    //Implicitly defined:
    def this.a
        def get
            ==> this.a
        def set(val?)
            ==> this.a = val or this.a
    //Function/method definitions are actually `get` definitions of themselves. To only define a setter, set the def get to return `nothing`:

    def this.setter_only_function(param="default")
        //Nested function definitions don't declare new scopes implicitly. You can do it by wrappring the body in a block.
        def set
            this.a = param
        def get
            ==> nothing

```
## Assignements de variables

### Normal

```kt

thing = "thinixkdk" //==> STR tjifbfjdn
STR thing = "tjifbfjdn" //==> STR "tjifbfjdn"
thing = 5 //=> Error: can't set the str "thing" to a num
```

### `hybrid`

```kt
hybrid thing = "jdjekskk" //==> STR "jdjekskk"
thing = 5 //==> NUM 5
```

### `volatile`

supprime la variable à la fin du bloc:

```kt
use stdout from io
my_str = "blablabla"
if yes
    volatile my_str = "bla"
    log my_str //==> STR~bla STR blablabla
    stdout my_str //bla
stdout my_str //blablabla

{
    // Une autre manière de créer un bloc. Plus succint que "if yes"
    volatile other = 3.1415
    log other  //==> STR~other
}
stdout other.to_str //UndefinedError: variable "other" is not defined here. Check for a potential "volatile" keyword
```

### Opérateur `->`

    thing = thing.uppercase
    <==>
    thing->uppercase

Ducoup TOUTES les fonctions/méthodes retournent des valeurs par défaut, dans les classes, this est return par la methode au lieu de muter this

## Opérateurs `and`, `not` et `or`
### Dans des conditions
servent de combinateurs classiques:
```kt
debug (yes and no) // yon no
debug (yes or no) // yon yes
debug (not no) // yon yes
```
### En tant qu'opérateurs de *control flow*
```kt
fun f1 ==> no
fun f2 ==> output "hehe"
// exécute f2 seulement si f1 existe ET renvoie une valeur truthy
f1 and f2 //==> yon no
// exécute f2 si f1 n'existe pas OU renvoie une valeur falsey.
f1 or f2 //hehe
```
 
## Types

### Opérateur `as`
Syntactic sugar pour la méthode `<type>.as_<type>`
Peut prendre un argument max.
(Pour plusieurs arguments, on utilise la méthode directement)

```kt
hybrid date = dat.now
output date as str "DD/MM/YYYY HH:mm"
date->as_str "DD/MM/YYYY HH:mm" // Marche seulement pour les varables hybrid
```

### **Dat**es
```kt
my_dat = 2019-08-14T08:07 or 2019-08-14 or 08:06
//==> dat 2019-08-14T08:07
my_month = ANY-09-ANY // represents just a month of any year, of any day
```

Initialization: `dat.now`
Format: `[<year>-<month>-<day>]T[<hours>:<minutes>]`
#### Methods
- `date.`

### **Num**bers (int==float)

```kt
my_num = 1000 or 0.458 or 10_000_000
```
Initialization: `0`
Format: `[<digit-or-underscore...>.]<digit-or-underscore...>`

### **Str**ings

```kt
my_str = "sample text" or `sample_text` // both are the same
my_str = 'sample_text' //SyntaxError
```
Initialization: `""`<br>
Format: `"<string>"`

### **R**e**g**ular e**x**pressions

```kt
my_rgx = ^[0-9\n]?.+(\s*)$ //==> RGX [0-9\n]?.+(\s*)
my_rgx = [\w_-]{3} //SyntaxError
my_rgx = ^$ // RGX (empty)
my_rgx = ^^\d+.?$$ //==> RGX ^\d.?$
```
Initialization: `^$`<br>
Format: `^<regular expression>$`

### **P**a**th**s

```kt
my_pth = /home/lol/machin //==> PTH /home/lol/machin
my_pth = ~/new/thingie //==> PTH ~/new/thingie
```
Initialization: `pth.cwd or pth.home` (Current working directory of terminal, or fallback to home directory)<br>
Format: `<"~" || "/"><identifier-friendly-or-string>/[<identifier-friendly-or-string>/[<identifier-friendly-or-string>/...]]`


### **Arr**ays
Arrays are just maps with incrementing numbers starting from 0 as keys.

```kt
my_arr = ["kfid", 9394, [ 8,88,8]]
ARR[STR] my_arr = ["kdkd" 666] //TypeError: my_arr is an array of strings, can't add NUM 666 to it.
arr2 = [] //==> ARR (empty)
arr2->to_y //==> YON no
```
Initialization: `[]`<br>
Format: `[<open>][<value>[, <value>[, <value> ...]]][<close>]` <br>(`<close> = "]", <open> = "["`)

### **Map**s

```kt
my_map = key: 8383, "key-2": "jdkdk" //==> MAP key: 8383, key-2: "jdkdk"
my_map.key //==> NUM 8383
my_map.key-2 //==> NUM 8381
my_map."key-2" //==> STR jdkdk
my_map->key //==> NUM 8383
my_map //==> NUM 8383

map2 = [:] //==> MAP (empty)
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
Initialization: `no` <br>
Format: `<"yes" || "no">`

### `nothing`

Représente une valeur nulle.

```kt
my_nothing = nothing //==> NOTHING
log("b" if my_nothing else "a") //b
my_nothing is nothing //==> YON yes
```

Initialization: `nothing` <br>
Format: `nothing`

### **Col**ors
```kt
my_col = #268CCE0A // RGBA
my_col = #268cce
my_col = blue //Undefined: Variable `blue` is not defined
```

Initialization: `#00000000` (`#00000000.to_rgba_str //==> rgba(0,0,0,0)`)<br>
Format: `#<hexadecimal-digit * 6>`

## Fonctions anonymes

Quand une méthode/fonction accepte un argument de type `FUN` (function), il suffit de passer un bloc comme argument, et celui ci sera executé. La valeur de retour est automatique

```kt
map =
    id: 4, // comma not necessary when linebreak
    key: "tess"
//==> MAP id: 4, key: "tess"

ARR[MAP[STR]] map2 = [
    [key: "ew", color: "#363637"],
    [key: "en", color: "#000000"],
    [key: "al", color: "#00FF00"]
]

map2->to_arr { this.key if  }
//==> ARR "ew", "en"



map2 //==> ARR "ew", "en"
```

Exemple

```kt
class map
    //...

    def this.to_arr(FUN mapper) // fun = function
         ARR array = []
         each this
             //key & value defined automatically by `each`
             array += mapper this
        ==> array // return == "==>"
```

## Classes

mot clé "new" pour instancier.

`new Thing` <===> `Thing.new()`

les args passés à new sont automatiquement mis comme props de l'instance, si non utilisé par la méthode new

ex:

```kt
class MY_CLASS
    def new(a, b, c, d=5)
        this.d = nothing
        this.a = b unless b == 3

new MY_CLASS(1,4,6)
//==> MY_CLASS a: 4, c: 6
new MY_CLASS(1,3,8)
//==> MY_CLASS c: 8
```

## Exemple de code

TIP: Fait partie des `builtins`

```kt
use * from io // Imports every `io` symbol, but pollutes the namespace. `use io` would not (it'd prefix them with io::)
use term

class file:
    def new(PTH path)
        this.path = path

    def this.contents
        ==> read path //Will search for `path` then `this.path`

    def this.valid_encoding?
        encoding->file.encoding // equivalent to this.encoding = file.encoding
        with encoding
            when "utf8" or "utf-8"
                encoding->"utf8"
                ==> yes
            when "ascii"
                ==> yes
            else
                ==> no


    def this.log(color?) // Équivalent à "color=nothing"
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

## Builtins

Method names starting with `__` are internal methods
### `num`'s
- `.ordinal(|short, prefix)` (`4.ordinal` => `fourth` || `3.14.ordinal|short` => `3rd` || `3.95.ordinal|prefix` => `th`) Will apply `.round` to the number
- `.floor`
- `.ceil`
- `.round(num:precision?)` Will round the number to `precision` decimal places (0 = a whole number)
- ~~`.fixed(num:digits)`~~ Use `str.pad(characters, "0")` instead
### `str`'s

#### Case convertions
Any case can be converted to any case.

List of case convertions methods:
- `.uppercase` (`some string` => `SOME STRING`)
- `.lowercase` (`some string` => `some string`)
- `.title_case` (`some string` => `Some String`)
- `.kebab_case` (`some string` => `some-string`) can be used w/ `.ascii` to 'slugify' strings for urls
- `.snake_case` (`some string` => `some_string`) can be used w/ `.uppercase` to make UPPER_SNAKE_CASE
- `.camel_case` (`some string` => `someString`) can be used w/ `.uppercase` to make PascalCase
- `.upperfirst` (`some string` => `Some string`)
- `.random_case` (`some string`=> `sOMe StRiNg`)
- `.alternate_case(|uppercase_first)` (`"some string".alternate_case` => `sOmE StRiNg` || `"some string".alternate_case|uppercase_first` => `SoMe StRiNg`) will skip over non-uppercasable characters (eg. Here the space is not uppercasable, so the letter next to it will be uppercase, not lower)

#### Others
- `.ascii(str:replacement="-")` replaces accents with their ascii counterpart, and removes non-convertible characters, to replace them with `replacement`
- `.replace(str:search, str:replacement, num:max_replacements?)`
- `.remove(str:search, num:max_removals?)`
- `.trim(str:using=" "|start, end)`
- `.length`
- `.pad(num:characters, str:using|start, end)` Will pad a `str` to `characters` characters, using the `using` str. Will pad to the left (`start`), to the end (`end`) or to the center (`start` _and_ `end`)


### `arr`'s
Arrays are not really a type, so all the map methods are also applicable to arrays.

### `map`'s
Note: function that takes "items" as arguments take a map that looks like [key: (the key), value: (the value)]
- `.sort (fun sort_fun?)` sorts alphabetically by default. the function takes two items: the previous item and the current being iterated over. The function must return a `yon`
- `.transform (fun transformer)` iterates through the array and replace each value with the result of `transformer(item)`
- `.shuffle (fun shuffler?)` `shuffler`: takes the current item as an argument, and shuffles it if the function returns `yes`. By default, this function is as follows: `fun shuffler = (item) ==> yes`
```kt
my_arr = [1, 5, 69, 2, 1, 5980]
my_arr->shuffle item ==> item.value != 1 //==> [1, 5980, 2, 5, 1, 69]
```
Note: This does *not* change the map/array's keys, but changes the `index`—an internal property to each map/array item. This allows maps to have an order that can be changed. The same thing occurs with `.sort`
- `.length` 
- `.deduplicate`
- `.keys ` or `.indexes` (`.indexes` is an alias)
- `.values` 
- `.__indexes` internal property that each item has. 
