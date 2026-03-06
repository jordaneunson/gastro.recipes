# gastro.recipes

Recipes written as pseudocode. Not a cookbook — a codebase.

## The Language

**gastro** — a loosely typed, kitchen-oriented pseudolanguage. The syntax borrows from shell scripting, C-like blocks, and whatever felt right at the time.

### File Format

- **Extension:** `.txt`
- **Encoding:** UTF-8
- **Naming:** Recipe name in plain English, spaces are fine
- **Indentation:** 4 spaces

### The Canonical Recipe

The bolognese is the reference implementation:

```
#!/bin/gastro
# Servings: 4

saucePan(mediumHeat) {
    add: equalParts(_veal, _beef, _pork)
    fry: equalParts
    add: _redWine(enough)
    wait: 30seconds
}

saucePan(mediumHeat) {
    add: _onion(1/2), _carrot(1), _celeryStalk(1)
    blitz
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

### Syntax Reference

#### Shebang
```
#!/bin/gastro
```
Every recipe starts with this. Always `#!/bin/gastro` — the dish name comes from the filename.

#### Comments
```
# Servings: 4
# this is the roux, don't burn it
```
Standard `#` comments. Used for servings, notes, warnings, and editorial.

#### Equipment Blocks
The primary structure. An equipment name with heat/temp as arguments. Contents are the steps:
```
dutchOven(mediumHeat) {
    _onion(1){diced}
    _garlic(3){minced}
    cook(5mins)
}
```

Equipment names are camelCase: `saucePan`, `dutchOven`, `largeSkillet`, `castIron`, `wok`, `bakingDish`, `stoneSkillet`, `prepCounter`.

Heat arguments: `mediumHeat`, `highHeat`, `lowHeat`, `mediumHighHeat`, `mediumLowHeat`, `noHeat`.

Temperature arguments: `400F`, `350F`, `325F`.

Combined: `dutchOven(325F, lidOn)`, `saucePan(lowHeat, lid-on)`.

The same equipment can appear multiple times — each block is a phase.

#### Ingredients
Prefixed with underscore. Quantities in parens. Modifiers in braces:
```
_chickenThighs(2lbs){cubed}
_onion(1){diced}
_garlic(4cloves){minced}
_redWine(enough)
_truffle(as much as u can afford)
```

Quantities are informal: `enough`, `generous`, `glug`, `a tad`, `the whole god damn package`.

#### Actions
Always colon syntax — `action: target`:
```
fry: equalParts
add: _redWine(enough)
garnish: _shreddedParm, _fineParsley
mix: all
toss: _spaghetti, _sauce
cook: _spaghetti
wait: 40mins
sear: allSides
simmer: 40mins
drain: fat
```

No-argument actions stand alone:
```
flip
remove
deglaze
slice
```

#### Plating / Serving
```
plate {
    toss: _spaghetti, _sauce
    garnish: _shreddedParm, _fineParsley
}
```

#### Control Flow
```
while(steak != done) {
    wait(30seconds)
    flip()
}
```

#### Operators
```
_pasta || _polenta          # either/or
equalParts(_veal, _beef, _pork)
```

### Full Language Spec

See [LANGUAGE.md](LANGUAGE.md) for the complete specification and grammar.

## Contributing a Recipe

1. Create a `.txt` file named after the dish
2. Start with `#!/bin/gastro`
3. Write it in gastro — use the bolognese as your guide
4. 4-space indentation
5. Commit and push

## License

These recipes are public. Cook them. If they're bad, that's on you.
