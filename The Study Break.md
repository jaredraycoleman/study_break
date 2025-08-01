The Study Break is our cozy backyard bar tucked beneath the trees, lit by string lights and a glowing neon "cocktails" sign. With a curated selection of spirits, Adirondack seating, and a fire pit, it's the perfect place to unwind, sip, and reset.
## Full Menu

```dataview
table without id file.link as "Cocktail", description as "Description"
from "Cocktails"
where type = "cocktail"
sort file.name asc
```

## Available Menu

```dataviewjs
// Step 1: Get in-stock ingredient paths
const stocked = dv.pages('"Ingredients"')
  .where(p => p.in_stock)
  .map(p => p.file.path); // e.g. "Personal/Bar/Ingredients/Gin.md"

// Step 2: Get cocktails with valid ingredients
const cocktails = dv.pages('"Cocktails"')
  .where(p => p.type === "cocktail" && Array.isArray(p.ingredients));

// Step 3: Filter cocktails where every ingredient is in the stocked list
const fullyStocked = cocktails.where(c =>
  c.ingredients.every(link => stocked.includes(link.path))
);

// Step 4: Render the table
dv.table(
  ["Cocktail", "Description", "Ingredients"],
  fullyStocked.map(c => [
    c.name ?? c.file.name,
    c.description ?? "—",
    c.ingredients.map(i => i.toString())
  ])
);
```
