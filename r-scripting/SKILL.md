---
name: r-scripting
description: R scripting conventions. Apply whenever writing, editing, or reviewing any R code.
user-invocable: false
---

# R Scripting Conventions

Always follow these rules when writing any R code:

## Assignment

Always use `=` instead of `<-` for all assignments — variables, function results, intermediate objects, everything.

```r
data = read.csv("data.csv")
result = lm(y ~ x, data = df)
```

## Piping

Always use the native pipe `|>` instead of `%>%` whenever possible.

```r
df |> filter(x > 0) |> summarise(mean_y = mean(y))
```

## Spacing around function calls and control flow

Do not add a space before `(` in function calls, function definitions, or control flow keywords:

```r
# correct
function(){

}

if(condition){

}

for(i in seq){

}

mean(x)
```

```r
# wrong
function () { }
if (condition) { }
for (i in seq) { }
mean (x)
```

## Comments

- Keep comments minimal — one short inline comment at most per logical block
- Only add a comment if the logic is genuinely non-obvious; omit otherwise
- Do not write block comments or multi-line comment headers
