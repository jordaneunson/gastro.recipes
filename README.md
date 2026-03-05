# masticable.eng

Recipes written as pseudocode. Not a cookbook — a codebase.

## The Language

Recipes are written in a loosely typed, kitchen-oriented pseudolanguage. The syntax borrows from shell scripting, C-like blocks, and whatever felt right at the time.

### File Format

- **Extension:** `.txt`
- **Encoding:** UTF-8
- **Naming:** Recipe name in plain English, spaces are fine

### Syntax Reference

#### Shebang (optional)
```
#!/bin/bolognese
```
Not required. Some recipes have it, some don't. When present, it names the dish.

#### Comments
```
# Servings: 4
# this is the roux, don't burn it
```
Standard `#` comments. Used for servings, notes, warnings, and editorial.

#### Equipment Blocks
The primary structure. An equipment name acts as a function call with heat/temp as arguments. Contents are the steps:
```
dutchOven(mediumHeat) {
    _onion(1){diced}
    _garlic(3){minced}
    cook(5mins)
}
```

Equipment names are camelCase: `saucePan`, `dutchOven`, `largeSkillet`, `castIron`, `wok`, `bakingDish`, `stoneSkillet`.

Heat arguments: `mediumHeat`, `highHeat`, `lowHeat`, `mediumHighHeat`, `mediumLowHeat`, `noHeat`.

Temperature arguments: `400F`, `350F`, `325F`.

Combined: `bake(400F, 10mins)`, `dutchOven(325F, lidOn)`.

#### Ingredients
Prefixed with underscore. Arguments are quantities. Braces are prep instructions:
```
_chickenThighs(2lbs){cubed}
_onion(1){diced}
_garlic(4cloves){minced}
_redWine(enough)
_truffle(as much as u can afford)
```

Quantities are informal: `enough`, `generous`, `glug`, `a tad`, `the whole god damn package`.

#### Actions
Function-style calls for cooking operations:
```
sear(allSides)
simmer(40mins)
deglaze()
stir(untilMelted)
cook(tillSoft)
flip()
remove()
drain(fat)
blitz(half)
```

#### Prep Functions
Named functions that return prepared sub-components:
```
marinade() {
    _yogurt(1cup)
    _ginger(1tbsp){grated}
    _garamMasala(1tbsp)
}

prep() {
    _chickenThighs(2lbs){cubed}
    coat(marinade())
    refrigerate(1hour)
}
```

#### Control Flow
Occasional loops and conditionals:
```
while(steak != done) {
    wait(30seconds)
    flip()
}

while(!alDente) {
    add: _broth(1ladle)
    stir()
    wait(untilAbsorbed)
}

while ( taste = poor )
    season
```

#### Colon Syntax (alternate)
Some recipes use `key: value` instead of function calls. Both are valid:
```
fry: equalParts
add: _redWine(enough)
garnish: _shreddedParm, _fineParsley
```

#### Plating / Serving
```
plate {
    toss: _spaghetti, _sauce
    garnish: _shreddedParm, _fineParsley
}

serve() {
    _basmatiRice
    _naanBread
    garnish: _cilantro
}
```

#### Operators
```
_pasta || _polenta          # either/or
( beef|veal|pork )          # choose from
```

### Style Variations

The codebase is intentionally inconsistent — recipes were written as they came to mind. You'll find:

- **Structured recipes** — full shebang, proper equipment blocks, underscore-prefixed ingredients (bolognese, butter chicken, cacciatore)
- **Loose recipes** — minimal syntax, plain English mixed with pseudocode (roast chicken, ice cream, lemon ricotta ravioli)
- **Hybrid recipes** — starts structured, devolves into prose when things get complicated (meatloaf macaroni, tater tot slop)
- **Numbered steps** — some recipes use `Step 1 —` style instead of blocks (found in related mash recipes, not in this repo)

All are valid. The language adapts to the cook's state of mind.

### Indentation

Four spaces. Some older recipes use tabs. Don't mix them in new recipes.

## Contributing a Recipe

1. Create a `.txt` file named after the dish
2. Write it in the pseudolanguage (or don't — plain English is fine too)
3. Commit and push

There's no linter. There's no spec. Cook first, document second.

## License

These recipes are public. Cook them. If they're bad, that's on you.
