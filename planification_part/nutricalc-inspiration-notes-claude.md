```yaml
project: django_learning_project
inspired_by: NutriCalc  # food logging + calorie/nutrition breakdown app
scope: inspiration_only # NOT a clone; NutriCalc = reference for data model/features only
purpose: vehicle_for_django_practice # models/forms/ORM/views/templates

apps:
  home:
    role: public_display
    owns: [home.html, base.html]
    contains: sidenav
    sidenav_links: [home, lookup, build_a_meal]

  lookup:
    final_name: Lookup  # aliases seen in convo: "look item up", "look-up site" -> deprecated
    role: search_engine + item_detail
    data: every_loggable_item
    flow: search_input -> relevant_results_list_below -> click result -> item_detail_page
    item_detail_page: renders nutrition_template (single_item variant)
    receives_links_from: nutrition_template (per-ingredient drilldown)

  build_a_meal:
    role: meal_composer
    flow:
      - type_ingredient_name
      - select_from_relevant_list  # search-style like lookup
      - select_unit_of_measure
      - select_amount
      - click_add -> saved to running list displayed above input
    on_done: trigger nutrition_calculation (meal variant, n items)
    state: temporary # session-based, no permanent save by default
    optional: small_db_of_popular_preset_recipes # quickstart, separate from temp state
      open_q: own_model_vs_reuse_meal_model_with_is_preset_flag

  nutrition:
    is_full_app: false
    decision: render_only # no models, no dedicated routes beyond render
    role: shared_results_layer between lookup(single) and build_a_meal(multi)
    templates:
      single_item: 1 item breakdown
      multi_item: n items, aggregated totals
    interaction:
      - show nutrient (e.g. energy) with unit + total
      - click nutrient -> expand per-ingredient contributions (as links)
      - click ingredient link -> navigate to lookup item_detail
    requirement: each ingredient in breakdown needs id/slug ref back to lookup entry

decisions_locked:
  - nutrition_is_template_not_app: true
  - meal_state_default: temporary/session
  - popular_recipes_db: optional_separate_from_temp_state
  - app_name_standard: lookup

open_questions:
  - item_model_schema: undefined
  - meal_model_schema: undefined  # ingredient = (item, unit, amount) tuple representation pre/post calc
  - ref_passing_mechanism: build_a_meal -> nutrition -> lookup  # ids vs slugs vs query params
  - preset_recipes_model: own_model vs meal_model+is_preset_flag

relation_to_roadmap: >
  Structure above gives django_roadmap.md practice exercises concrete shape
  (e.g. FoodEntry-like models instead of generic blog Post). Not a fixed spec;
  simplify if it blocks learning goals.
```
