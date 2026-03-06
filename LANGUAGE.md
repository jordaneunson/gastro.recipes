# gastro Language Specification

**gastro** — a domain-specific language for writing recipes as code.

Version: 0.2.0

---

## 1. File Format

- **Extension:** `.txt`
- **Encoding:** UTF-8
- **Line endings:** LF or CRLF
- **Indentation:** 4 spaces (no tabs)

---

## 2. Shebang

Every recipe starts with:

```
#!/bin/gastro
```

The shebang is always `#!/bin/gastro` — it declares the file as a gastro recipe. The dish name comes from the filename, not the shebang.

---

## 3. Comments

Single-line only. Start with `#`.

```
# Servings: 4
# this is the roux, don't burn it
```

**Metadata comments** use `# Key: Value` syntax on lines immediately following the shebang:

```
#!/bin/gastro
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
| `prepCounter` | Prep surface (no heat) |

### 5.3 Heat Arguments

Heat is the first argument. Can be a named level or a temperature:

**Named levels:**
`lowHeat`, `mediumLowHeat`, `mediumHeat`, `mediumHighHeat`, `highHeat`, `noHeat`

**Temperatures:**
`325F`, `350F`, `375F`, `400F` (Fahrenheit assumed unless suffixed with `C`)

**Combined arguments:**
```
dutchOven(325F, lidOn)
largePot(highHeat, boilingWater)
saucePan(lowHeat, lid-on)
```

### 5.4 Sequential Blocks

The same equipment can appear multiple times. Each block represents a phase — a change in heat, a new stage, or a return to the vessel later:

```
saucePan(mediumHeat) {
    equalParts(_veal, _beef, _pork)
    fry: equalParts
    add: _redWine(enough)
    wait: 30seconds
}

saucePan(mediumHeat) {
    blitz: _onion(1/2), _carrot(1), _celeryStalk(1)
    fry: tillSoft
}

saucePan(highHeat) {
    add: _chickenBroth(enoughForSauce)
    add: _tomatoPaste(5.5oz)
    add: _rosemary(1sprig), _bayLeaf(2)
    mix: all
}
```

The equipment persists between blocks — contents carry forward unless explicitly `remove()`d.

---

## 6. Actions

Actions are function calls that represent cooking operations.

### 6.1 Colon Syntax

The preferred form. Clean, readable, and consistent:

```
fry: equalParts
add: _redWine(enough)
add: _chickenBroth(enoughForSauce)
mix: all
toss: _spaghetti, _sauce
garnish: _shreddedParm, _fineParsley
cook: _spaghetti
wait: 40mins
```

Multiple targets are comma-separated after the colon.

### 6.2 Function-Call Syntax

Also valid. Use when it reads more naturally:

```
cook(5mins)
simmer(40mins)
sear(allSides)
stir(untilMelted)
deglaze()
flip()
remove()
drain(fat)
blitz(half)
rest(10mins)
slice()
```

### 6.3 Action Arguments

- **Time:** `cook(5mins)`, `simmer(40mins)`, `wait(30seconds)`
- **Condition:** `stir(untilMelted)`, `cook(tillSoft)`, `cook(alDente)`
- **Target:** `drain(fat)`, `blitz(half)`
- **Scope:** `sear(allSides)`, `cook(5mins){eachSide}`

Both syntaxes are valid. Use whichever reads better — but favour colon syntax for clarity.

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

### 7.3 Nested Functions

Functions can nest inside equipment blocks:

```
prepCounter() {
    _steak() {
        #wash and trim
        _seasoning
    }
}
```

### 7.4 Common Function Names

| Name | Purpose |
|------|---------|
| `prep()` | Ingredient preparation before cooking |
| `marinade()` | Marinade composition |
| `breading()` | Coating/breading mixture |
| `cascadingFry()` | Sequential frying of ingredients in order |
| `equalParts()` | Equal portions of listed ingredients |
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

### 10.1 Plate Block

The standard recipe endpoint:

```
plate {
    toss: _spaghetti, _sauce
    garnish: _shreddedParm, _fineParsley
}
```

### 10.2 Serve Block

```
serve() {
    _basmatiRice
    _naanBread
    garnish: _cilantro
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

## 12. Canonical Example

The bolognese recipe demonstrates the ideal structure:

```
#!/bin/gastro
# Servings: 4

saucePan(mediumHeat) {
    equalParts(_veal, _beef, _pork)
    fry: equalParts
    add: _redWine(enough)
    wait: 30seconds
}

saucePan(mediumHeat) {
    blitz: _onion(1/2), _carrot(1), _celeryStalk(1)
    fry: tillSoft
}

saucePan(highHeat) {
    add: _chickenBroth(enoughForSauce)
    add: _tomatoPaste(5.5oz)
    add: _rosemary(1sprig), _bayLeaf(2)
    mix: all
}

saucePan(lowHeat, lid-on) {
    wait: 40mins
    add: _milk(1/2cup)
}

largePot(highHeat, boilingWater) {
    cook: _spaghetti
}

plate {
    toss: _spaghetti, _sauce
    garnish: _shreddedParm, _fineParsley
}
```

Key patterns:
- `#!/bin/gastro` shebang — always the same
- Metadata as comments (`# Servings: 4`)
- Equipment blocks with heat as first argument
- Same equipment reused across phases with different heat
- Colon syntax for actions (`fry:`, `add:`, `mix:`, `toss:`, `garnish:`)
- Ingredients always prefixed with `_`
- `plate` block as endpoint
- 4-space indentation throughout
- Clean, readable, minimal

---

## 13. Style Guide

1. **Shebang** — always `#!/bin/gastro`.
2. **Ingredient prefix** — always `_`. No exceptions.
3. **Equipment** — camelCase. Heat in first argument.
4. **Quantities** — in `()`. Be as precise or vague as the recipe demands.
5. **Modifiers** — in `{}`. Comma-separated if multiple.
6. **Actions** — prefer colon syntax (`add: _thing`) for readability.
7. **Comments** — use freely. Especially for warnings and timing notes.
8. **Indentation** — 4 spaces inside blocks. Nested blocks get another 4.
9. **Naming** — files named after the dish, plain English, `.txt` extension.
10. **Plain English** — always acceptable in comments and descriptive quantities. The language adapts to the cook.

---

## 14. Grammar (informal BNF)

```
recipe        ::= shebang? metadata* (block | statement)*
shebang       ::= '#!/bin/gastro'
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
action        ::= name '(' args? ')' modifier? | name ':' value (',' value)*
reference     ::= '+' name '(' ')'
comment       ::= '#' text
args          ::= value (',' value)*
name          ::= [a-zA-Z][a-zA-Z0-9]*
identifier    ::= [a-zA-Z][a-zA-Z0-9_-]*
```

---

## 15. Reserved Words

`serve`, `plate`, `garnish`, `prep`, `bam`, `while`, `when`, `add`, `remove`, `wait`, `equalParts`, `cascadingFry`

---

## 16. Future

- Syntax highlighting (VS Code, Sublime)
- `gastro lint` — validate recipe structure
- `gastro render` — output formatted recipe card (HTML, terminal, PDF)
- `gastro convert` — import from plain text / Markdown recipes
- Nutritional data lookup from ingredient names
- Timing calculator from `wait()` and `cook()` calls

---

*gastro v0.2.0 — cook first, document second.*
