
```dataviewjs
// Step 1: Get all in-stock ingredient paths
const stocked = dv.pages('"Personal/Bar/Ingredients"')
  .where(p => p.in_stock)
  .map(p => p.file.path); // raw path strings

// Step 2: Get all cocktails with ingredient links
const cocktails = dv.pages('"Personal/Bar/Cocktails"')
  .where(p => p.type === "cocktail" && Array.isArray(p.ingredients));

// Step 3: Create a map: ingredient path → list of cocktails that use it
const ingredientToCocktails = new Map();

for (let c of cocktails) {
  for (let ing of c.ingredients) {
    if (!ing?.path) continue;

    const key = ing.path;
    if (!ingredientToCocktails.has(key)) {
      ingredientToCocktails.set(key, []);
    }
    ingredientToCocktails.get(key).push(c.file.link);
  }
}

// Step 4: Identify missing ingredients
const missingIngredients = Array.from(ingredientToCocktails.keys())
  .filter(p => !stocked.includes(p));

// Step 5: Render as table
dv.table(
  ["Missing Ingredient", "Used In Cocktails"],
  missingIngredients.map(path => [
    dv.fileLink(path),
    ingredientToCocktails.get(path)
  ])
);

```

