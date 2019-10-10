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
- *Optional typed* `arg:str?`<br>Defaults to the type's [initialization value](#types)
- *Other arguments* `...options`—Registers `options` as a `[name:value]` map containing args whose name don't match arguments in signature

### Flags
- *Defaults to `yes`* : `!flag_name`
- *Defaults to `no`* : `flag_name`

### Example

NOTE: this is the recommended function declaration code style when the combined count of arguments and flags is greater than 4 (`...` operator counts as an argument)
```kt
use stdout from io
use term

fun colored_output(
    message:str, end="\n", color:col?, ...prefixes
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
### Omitting parenthesis
If you call a function with arguments or flags, you can only ommit parenthesis if you aren't chaining functions:

```kt
func1.func2.func3 //OK
func1 param //OK
func1 param.func2 //SyntaxError
func1 func2 // Resolves as func1(func2())
```
### Named parameter shortcut
If you have a variable whose name is the same as a function's argument, 
you can call it without using some stupid `long_argument_name: long_argument_name`: Just use `$long_argument_name`
### Flags-only shortcut
If you want to call a function but only need to activate/deactivate a flag, you simply do `function_name|flag1, flag2`
```kt
fun func = (a:num?, b:any=0.1 | flag) ==> log(a, b, flag)
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
func(a, b | flag)         //a = num 2, b = num 3, flag = yon yes
func|flag                 //a = num 0, b = num 0.1, flag = yon yes
func $b                   //a = num 0, b = num 3, flag = yon no
```
## Assignements de variables

### Normal

```kt

thing = "thinixkdk" //==> STR tjifbfjdn
STR thing = "tjifbfjdn" //==> STR "tjifbfjdn"
thing = 5 //=> Error: can't set the str "thing" to a num
```

### `Dynamic`

```kt
dynamic thing = "jdjekskk" //==> STR "jdjekskk"
thing = 5 //==> NUM 5
```

### `volatile`
Supprime la variable après un bloc *d'indentation*
```kt
use stdout from io
my_str = "blablabla"
{
    volatile my_str = "bla"
    log my_str //==> STR~bla STR blablabla
    stdout my_str //bla
}
stdout my_str //blablabla

{
    volatile other = 3.1415
    log other  //==> STR~other
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
// In the real world, use str.alphabet
my_str = "abcdefghijklmnopqrstuvwxyz"
arr[num] my_arr = my
```

### Opérateur `is` ou `is  a`
Retourne `yes` si `my_var` est du type `type`:
```
my_var = 2.51
my_var is a num //==> yes
my_var is a str //==> no
```

NOTE: Vous *pouvez* utiliser les noms de types comme variables, parce que `num`, `str`, ... représente un type seulement...
- lors d'un assignement typé (`pth my_pth = ...`)
- lors d'une déclaration de valeur typée (`〈pth ...〉`)
- dans le contexte de l'opérateur `as`
- dans le contexte de l'opérateur `is`

### **Dat**es
```kt
my_dat = 2019-08-14T08:07 or 2019-08-14 or 08:06
//==> DAT 2019-08-14T08:07
```

Initialization: `dat.now`<br>
Format: `[<year>-<month>-<day>]T[<hours>:<minutes>]`

### **Num**bers (int==float)

```kt
my_num = 1000 or 0.458 or 10_000_000
```
Initialization: `0`<br>
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
#### Initialization
`pth.cwd or pth.home` (Current working directory of terminal, or fallback to home directory)

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


### **Arr**ays

```kt
my_arr = ["kfid", 9394, [ 8,88,8]]
ARR[STR] my_arr = ["kdkd" 666] //TypeError: my_arr is an array of strings, can't add NUM 666 to it.
arr2 = [] //==> ARR (empty)
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
`a % b`  | *raises an error*            | 


### **Map**s

```kt
my_map = key: 8383, "key-2": "jdkdk" //==> MAP key: 8383, key-2: "jdkdk"
my_map.key //==> NUM 8383
my_map.key-2 //==> NUM 8381
my_map."key-2" //==> STR jdkdk
my_map->key //==> NUM 8383
my_map //==> NUM 8383

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

### **Col**ors
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

ARR[MAP[STR]] map2 = [
    [key: "ew", color: "#363637"],
    [key: "en", color: "#000000"],
    [key: "al", color: "#00FF00"]
]

map2->to_arr { this.key if != "al" } // same as this.key if this.key != "al"
//==> ARR "ew", "en"



map2 //==> ARR "ew", "en"
```

Exemple

```kt
class map
    //...

    def self.to_arr(FUN mapper) // fun = function
         ARR array = []
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
    fun new = (PTH path){}
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
avec des retours à la ligne littéraux et des $dollars〉 //str {mon contenu sans guillemets\\navec des retours à la ligne littéraux et des $dollars}

〈pth mon/chemin/〉 //==> pth mon/chemin
```

## Operator shorcuts `+=`, `++`, `-=`, `--`, `/=`, `%=`, `*=`, `?=` and `:=`

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
