# NutriCalc-Inspired Project — Planning Notes

## Context
This is a **Django learning project**. It is **inspired by** an app called NutriCalc (a food-logging app that lets users log food and generates a calorie/nutrition breakdown), but this project is **not** a clone or rebuild of NutriCalc. NutriCalc is only a reference point for data modeling and feature ideas.

These notes exist to guide the direction of the project as it's built out, and to sit alongside `django_roadmap.md` / `django_learning_progress.md` as a source of "what are we actually building and why."

---

## App Structure

### 1. Home App
- Public-facing, informational.
- `home.html` lives here and displays general information.
- `base.html` (the shared site template/shell) is built as part of this app.
- Contains the side navigation menu, which links to:
  - Home page
  - Look Item Up
  - Build a Meal

### 2. Lookup App
- Final name for this app: **Lookup** (previously referred to inconsistently as "look item up" / "look-up site" — now standardized).
- Holds information about every single loggable food item.
- Functions as a **search engine**: user searches for an item, relevant results/options are shown below the search bar.
- Clicking a result takes the user to that item's detail — which is where the Nutrition template gets rendered for a single item.

### 3. Build a Meal App
- Lets the user assemble a meal from multiple ingredients.
- Flow:
  1. Type an ingredient name.
  2. Select from a relevant list (autocomplete/search-style, similar to Lookup).
  3. Select a unit of measure.
  4. Select an amount.
  5. Click "Add" — the selection is saved to a running list displayed above the input area.
- Once done, the user can trigger a **nutrition calculation** for the whole meal.
- **State handling:** the meal is held **temporarily** (e.g. session-based) — no permanent save required by default.
- **Optional extra:** a small database of **popular/preset recipes** may exist separately, so users can quick-start from a preset instead of building from scratch every time.

### 4. Nutrition (not a full app — a shared template)
- Originally considered as its own app, but decided this only **renders data** passed to it — it doesn't own its own models or need its own routes/views beyond rendering.
- Acts as the shared results layer between **Lookup** (single item) and **Build a Meal** (multiple items).
- **Single item case:** one template showing the nutrition breakdown for one item.
- **Multi-item case (a meal):** a template that can handle **n items**, showing combined/aggregated totals.
- **Interaction / drill-down behavior:**
  - Each nutrient (e.g. "Energy") is shown with its unit and total amount.
  - Clicking a nutrient (e.g. "Energy") expands to show that nutrient's contribution **per ingredient**, displayed like a list of links.
  - Clicking one of those per-ingredient links sends the user to that ingredient's page in **Lookup**.
  - This means every ingredient shown in a breakdown needs to carry some kind of reference (ID/slug) back to its Lookup entry.

---

## Open Design Questions / Decisions Made

| Topic | Decision |
|---|---|
| Is Nutrition its own Django app? | No — it's a shared template (or set of templates/partials) rendered by Lookup and Build a Meal. No dedicated models. |
| Does a meal get permanently saved? | Not by default — meal state is temporary (session-based). |
| Are there any permanently saved meals? | Possibly — a small separate database of popular/preset recipes for quick-start convenience. |
| App naming | Standardized on **Lookup** (not "look item up" or "look-up site"). |

## Still Open / Worth Revisiting Later
- Exact data model for `Item`, `Meal`, and how "meal ingredients" (item + unit + amount) are represented before/after calculation.
- How ingredient references are passed from Build a Meal → Nutrition template → back to Lookup (IDs, slugs, or query params).
- Whether the "popular recipes" mini-database needs its own model, or reuses the Meal structure with a `is_preset` flag.

---

## Relationship to the Django Roadmap
This project is being used as a vehicle for practicing Django fundamentals (models, forms, ORM, templates, views). The NutriCalc-inspired structure above is meant to give the practice exercises a concrete, motivating shape — not to lock in a fixed feature spec. Feel free to simplify any part of this if it gets in the way of the actual learning goals.
