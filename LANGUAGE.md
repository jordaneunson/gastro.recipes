# .eng Language Specification

**masticable.eng** — a domain-specific language for writing recipes as code.

Version: 0.1.0

---

## 1. File Format

- **Extension:** `.txt` (future: `.eng`)
- **Encoding:** UTF-8
- **Line endings:** LF or CRLF
- **Indentation:** 4 spaces (tabs tolerated, don't mix)

---

## 2. Shebang

Optional. Declares the recipe name.

```
#!/bin/bolognese
```

The path after `#!` is always `/bin/` followed by the recipe identifier in camelCase or lowercase.

---

## 3. Comments

Single-line only. Start with `#`.

```
# Servings: 4
# this is the roux, don't burn it
```

**Metadata comments** use `# Key: Value` syntax on lines immediately following the shebang:

```
#!/bin/bolognese
# Servings: 4
# Time: 90mins
# Difficulty: medium
```

Reserved metadata keys: `Servings`, `Time`, `Difficulty`, `Source`.

---

## 4. Ingredients

Ingredients are the core data type. They are prefixed with `_` (underscore).

### 4.1 Basic Ingredient

```
_onion
```

### 4.2 Ingredient with Quantity

Quantity is expressed in parentheses `()`:

```
_onion(1)
_chickenBroth(6cups)
_butter(2tbsp)
_redWine(enough)
```

Quantities can be:
- **Numeric + unit:** `2lbs`, `1/2cup`, `500ml`, `3tbsp`, `1tsp`, `14oz`, `400ml`
- **Numeric only:** `1`, `2`, `4`, `1/2`
- **Descriptive:** `enough`, `generous`, `glug`, `pinch`, `dollop`, `tad`
- **Contextual:** `enoughForSauce`, `to cover`, `as much as u can afford`

### 4.3 Ingredient with Modifier

Modifiers describe preparation state. Expressed in braces `{}`:

```
_onion(1){diced}
_garlic(4cloves){minced}
_chickenThighs(2lbs){cubed, seasoned}
_ginger(2tbsp){grated}
```

Multiple modifiers are comma-separated inside braces:

```
_shortRibs{bonein, seasoned}
```

### 4.4 Full Ingredient Syntax

```
_name(quantity){modifier, modifier}
```

All parts except `_name` are optional:

```
_salt                          # just the ingredient
_salt(1tsp)                    # with quantity
_salt{generous}                # with modifier
_cream(1cup){cold}             # full form
```

### 4.5 Ingredient References

An ingredient defined earlier can be referenced with `{}` to indicate state:

```
_chicken{return}               # bring back previously removed chicken
_chicken{marinaded}            # chicken in its post-marinade state
_steak(rest)                   # action as pseudo-quantity
```

---

## 5. Equipment Blocks

Equipment blocks are the primary execution context — they represent a physical vessel at a specific heat setting.

### 5.1 Syntax

```
equipment(heat) {
    ...steps...
}
```

### 5.2 Equipment Names

camelCase. Common equipment:

| Name | Description |
|------|-------------|
| `saucePan` | Sauce pan |
| `largePot` | Large pot |
| `mediumPot` | Medium pot |
| `smallPot` | Small pot |
| `dutchOven` | Dutch oven |
| `largeSkillet` | Large skillet |
| `castIron` | Cast iron skillet |
| `stoneSkillet` | Stone skillet |
| `wok` | Wok |
| `fryPan` | Frying pan |
| `largePan` | Large pan |
| `bakingDish` | Oven baking dish |
| `bakingPan` | Baking pan |

### 5.3 Heat Arguments

Heat is the first argument. Can be a named level or a temperature:

**Named levels:**
`lowHeat`, `mediumLowHeat`, `mediumHeat`, `mediumHighHeat`, `highHeat`, `noHeat`

**Temperatures:**
`325F`, `350F`, `375F`, `400F` (Fahrenheit assumed unless suffixed with `C`)

**Combined arguments:**
```
dutchOven(325F, lidOn)
bake(400F, 10mins)
largePot(highHeat, boilingWater)
```

### 5.4 Sequential Blocks

The same equipment can appear multiple times. Each block represents a phase:

```
dutchOven(mediumHighHeat) {
    _oliveOil
    _shortRibs
    sear(allSides)
    remove()
}

dutchOven(mediumHeat) {
    _onion(1){chunked}
    _carrots(3){chunked}
    cook(5mins)
}
```

The equipment persists between blocks — contents carry forward unless explicitly `remove()`d.

---

## 6. Actions

Actions are function calls that represent cooking operations.

### 6.1 Basic Actions

```
cook(5mins)
simmer(40mins)
sear(allSides)
stir(untilMelted)
deglaze()
flip()
remove()
drain()
drain(fat)
boil(15mins)
bake(25mins)
broil(3mins){golden}
rest(10mins)
slice()
```

### 6.2 Action Arguments

- **Time:** `cook(5mins)`, `simmer(40mins)`, `wait(30seconds)`
- **Condition:** `stir(untilMelted)`, `cook(tillSoft)`, `cook(alDente)`
- **Target:** `drain(fat)`, `blitz(half)`
- **Scope:** `sear(allSides)`, `cook(5mins){eachSide}`

### 6.3 Colon Syntax (alternate)

Actions can also use `key: value` form:

```
fry: equalParts
add: _redWine(enough)
garnish: _shreddedParm, _fineParsley
mix: all
toss: _spaghetti, _sauce
```

Both function-call and colon syntax are valid. Use whichever reads better.

---

## 7. Functions

Named blocks that define reusable sub-components.

### 7.1 Definition

```
marinade() {
    _yogurt(1cup)
    _ginger(1tbsp){grated}
    _garamMasala(1tbsp)
}
```

### 7.2 Calling

Functions are called by name with `()`:

```
coat(marinade())
```

Or referenced with `+`:

```
wok(highHeat) {
    +prep()
    cook(3mins)
    +smallBowl()
}
```

### 7.3 Common Function Names

| Name | Purpose |
|------|---------|
| `prep()` | Ingredient preparation before cooking |
| `marinade()` | Marinade composition |
| `breading()` | Coating/breading mixture |
| `serve()` | Plating and garnish |
| `plate` | Alias for serve (block form) |

---

## 8. Control Flow

### 8.1 While Loop

```
while(!alDente) {
    add: _broth(1ladle)
    stir()
    wait(untilAbsorbed)
}
```

```
while(steak != done) {
    wait(30seconds)
    flip()
}
```

### 8.2 Conditional (inline)

```
when(_bacon){desired}
    _onion(1){diced}
```

The `when` keyword gates subsequent steps on a condition.

---

## 9. Operators

### 9.1 Or (`||`)

Either/or choice:

```
_pasta || _polenta
```

### 9.2 Pipe (`|`)

Choose from a set:

```
( beef|veal|pork )
equalParts(_veal, _beef, _pork)
```

### 9.3 Addition (`add`)

Introducing ingredients to the current context:

```
add: _chickenBroth(enoughForSauce)
add(smallPot)
```

---

## 10. Output

### 10.1 Serve Block

```
serve() {
    _basmatiRice
    _naanBread
    garnish: _cilantro
}
```

### 10.2 Plate Block

```
plate {
    toss: _spaghetti, _sauce
    garnish: _shreddedParm, _fineParsley
}
```

### 10.3 Inline Garnish

```
garnish {
    _cilantro
    _peanuts
}
```

### 10.4 Exit

```
bam()
```

Recipe complete.

---

## 11. Layers (Baking)

For layered assembly:

```
bakingDish(400F) {
    layer: _meatMixture
    layer: _mashedPotatoes
    fork(top){ridges}
    bake(25mins)
    broil(3mins){golden}
}
```

---

## 12. Style Guide

1. **Shebang** — include it. It names the dish.
2. **Ingredient prefix** — always `_`. No exceptions.
3. **Equipment** — camelCase. Heat in first argument.
4. **Quantities** — in `()`. Be as precise or vague as the recipe demands.
5. **Modifiers** — in `{}`. Comma-separated if multiple.
6. **Comments** — use freely. Especially for warnings and timing notes.
7. **Indentation** — 4 spaces inside blocks.
8. **Naming** — files named after the dish, plain English, `.txt` extension.
9. **Plain English** — always acceptable. If pseudocode doesn't serve the recipe, write prose. The language adapts to the cook.

---

## 13. Grammar (informal BNF)

```
recipe        ::= shebang? metadata* (block | statement)*
shebang       ::= '#!/bin/' identifier
metadata      ::= '#' key ':' value
block         ::= (equipment | function | output | control) '{' statement* '}'
equipment     ::= name '(' args ')'
function      ::= name '(' ')'
output        ::= 'serve' '(' ')' | 'plate' | 'garnish'
control       ::= 'while' '(' condition ')' | 'when' '(' condition ')'
statement     ::= ingredient | action | comment | block | reference
ingredient    ::= '_' name quantity? modifier?
quantity      ::= '(' value ')'
modifier      ::= '{' value (',' value)* '}'
action        ::= name '(' args? ')' modifier? | name ':' value
reference     ::= '+' name '(' ')'
comment       ::= '#' text
args          ::= value (',' value)*
name          ::= [a-zA-Z][a-zA-Z0-9]*
identifier    ::= [a-zA-Z][a-zA-Z0-9_-]*
```

---

## 14. Reserved Words

`serve`, `plate`, `garnish`, `prep`, `bam`, `while`, `when`, `add`, `remove`, `wait`

---

## 15. Future

- `.eng` file extension
- Syntax highlighting (VS Code, Sublime)
- `eng lint` — validate recipe structure
- `eng render` — output formatted recipe card (HTML, terminal, PDF)
- `eng convert` — import from plain text / Markdown recipes
- Nutritional data lookup from ingredient names
- Timing calculator from `wait()` and `cook()` calls

---

*masticable.eng v0.1.0 — cook first, document second.*
