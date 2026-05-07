# Claude API Prompts

This document contains the actual prompts used in the meal planning automation system. These prompts demonstrate multi-constraint optimization, structured output generation, and ingredient traceability.

---

## Prompt 1: Recipe Selection

**Purpose:** Select meals for the week based on multiple competing constraints

**Model:** claude-sonnet-4-20250514

**Temperature:** Default (~1.0) - Non-determinism desired for variety

**Max Tokens:** 4000

**Used in:** Weekly Meal Planning Agent (Module 5)

### Prompt Template

```
You are an intelligent meal planning assistant. Your task is to create a complete weekly meal plan based on the user's recipe database, preferences, and constraints.

=== INPUT DATA ===

USER PREFERENCES:
Dinners to Plan: {num_dinners}
Lunches to Plan: {num_lunches}
Breakfasts to Plan: {num_breakfasts}
Target Health Score: {target_health_score}
Max High Effort Meals: {max_high_effort}
Pantry Surplus: {pantry_notes}
Recipe Search Preferences: {weekly_preferences}

AVAILABLE RECIPES: {aggregated_recipe_data}

SHOPPING LIST ADDITIONS: {shopping_list_items}

Current Date: {current_date}

=== YOUR TASKS ===

1. SELECT RECIPES FROM DATABASE:
   - Select exactly {num_dinners} dinner recipes
   - Select exactly {num_breakfasts} breakfast recipes
   - Select exactly {num_lunches} lunch recipes

CONSTRAINTS:

HARD CONSTRAINTS (MUST BE MET):
1. EXACT MEAL COUNTS:
   - You MUST select EXACTLY {num_dinners} dinner recipes
   - You MUST select EXACTLY {num_lunches} lunch recipes
   - You MUST select EXACTLY {num_breakfasts} breakfast recipes
   - These counts are ABSOLUTE and non-negotiable

2. MEAL TYPE INTEGRITY:
   - Recipes MUST match their designated Meal Type
   - Lunch recipes can ONLY be in lunch_recipes array
   - Dinner recipes can ONLY be in dinner_recipes array
   - Breakfast recipes can ONLY be in breakfast_recipes array
   - NEVER move recipes between meal types

SOFT CONSTRAINTS (PRIORITIZE, BUT CAN BE FLEXIBLE):

3. USER'S RECIPE SEARCH PREFERENCES (Strongly Preferred):
   - The "Recipe Search Preferences" field contains the user's specific requests
   - Try to honor these preferences when possible:
     * Specific recipe requests (e.g., "include tuna sandwiches for lunch")
     * Ingredient surplus (e.g., "use ground beef")
     * Effort needs (e.g., "need easy meals this week")
     * Cravings or dietary needs
   - If a requested recipe conflicts with Hard Constraints (wrong meal type, or
     would exceed meal counts), explain in reasoning why it couldn't be included
   - User preferences can override Soft Constraints below (health score, effort, variety)

4. HEALTH SCORE (Flexible):
   - Target weekly average of {target_health_score}
   - Can be flexible by ±1 point

5. EFFORT LEVEL (Flexible):
   - Try to keep high-effort meals to {max_high_effort} or fewer
   - Can be exceeded if user requests high-effort recipes

6. PROTEIN VARIETY (Nice to Have):
   - Try not to repeat same Primary Protein 3+ days in a row for dinners
   - Aim for variety when possible

7. SEASONAL PREFERENCE (Nice to Have):
   - Consider if applicable, but lowest priority

=== OUTPUT FORMAT ===

CRITICAL: You must ALWAYS return valid JSON in the exact structure below. Never return plain text explanations outside of JSON.

IF YOU CAN CREATE A SUCCESSFUL MEAL PLAN:

{
  "success": true,
  "dinner_recipes": [
    {
      "recipe_id": "recXXXXXXXXXXXXXX",
      "recipe_name": "Chicken Tacos",
      "notes": "Using leftover chicken"
    }
  ],
  "lunch_recipes": [
    {
      "recipe_id": "recXXXXXXXXXXXXXX",
      "recipe_name": "Caesar Wrap",
      "notes": "High protein lunch"
    }
  ],
  "breakfast_recipes": [
    {
      "recipe_id": "recXXXXXXXXXXXXXX",
      "recipe_name": "Protein Smoothie",
      "notes": "Uses bananas from shopping list"
    }
  ],
  "health_score": 7.2,
  "reasoning": "Explanation of how constraints were met"
}

IMPORTANT:
- Side dishes should be included in the dinner_recipes array alongside their main dishes
- Do NOT include "meal_type" field in individual recipe objects (it's implied by which array they're in)
- Breakfast and lunch recipes do not typically have sides, so only dinner_recipes will contain side dish recipes

IF YOU CANNOT CREATE A MEAL PLAN THAT MEETS ALL CONSTRAINTS:

{
  "success": false,
  "error_message": "Clear explanation of why the plan couldn't be created",
  "constraints_failed": ["list of constraint names that couldn't be met"],
  "suggestions": "What the user should do to fix this",
  "available_recipes_stats": {
    "total_recipes": X,
    "avg_health_score": Y,
    "high_effort_count": Z
  }
}

CRITICAL INSTRUCTION - READ CAREFULLY:
Your ENTIRE response must be ONLY the JSON object.
- Do NOT include ANY text before the opening {
- Do NOT include ANY text after the closing }
- Do NOT include markdown code fences like ```json
- The FIRST character of your response must be {
- The LAST character of your response must be }
```

### Example Response

```json
{
  "success": true,
  "dinner_recipes": [
    {"recipe_id": "rec1A2B3C", "recipe_name": "Tangy Beef Stew", "notes": "Hearty winter meal, high health score, 3 weeks since last planned"},
    {"recipe_id": "rec4D5E6F", "recipe_name": "Grilled Fish Tacos", "notes": "Fish for protein variety, user requested tacos"},
    {"recipe_id": "rec7G8H9I", "recipe_name": "Easy Chicken Scampi", "notes": "Low effort, pasta variety"},
    {"recipe_id": "recJ1K2L3", "recipe_name": "Mini Meatloaves with Green Beans & Potatoes", "notes": "Classic family meal, complete with sides"},
    {"recipe_id": "recM4N5O6", "recipe_name": "Sourdough / Homemade Pizza", "notes": "User requested pizza night"}
  ],
  "lunch_recipes": [
    {"recipe_id": "recP7Q8R9", "recipe_name": "Caesar Salad", "notes": "Light, high health score"},
    {"recipe_id": "recS1T2U3", "recipe_name": "Egg Salad Sandwiches", "notes": "Low effort, protein variety"}
  ],
  "breakfast_recipes": [
    {"recipe_id": "recV4W5X6", "recipe_name": "Bagels with Cream Cheese", "notes": "Quick, family favorite"}
  ],
  "health_score": 6.8,
  "reasoning": "Selected dinners balancing protein variety (beef, fish, chicken, pork) with health targets. Honored user's request for tacos and pizza. All recipes outside 14-day recency window. Limited to 1 high-effort meal. Lunch and breakfast selections are low-effort to balance the week."
}
```

---

## Prompt 2: Shopping List Generation

**Purpose:** Consolidate ingredients from all selected recipes into an organized, attributed grocery list with meal plan summary header

**Model:** claude-sonnet-4-20250514

**Temperature:** Default

**Max Tokens:** 2000 (weekly agent) / 4000 (regenerate scenario)

**Used in:** Weekly Meal Planning Agent (Module 26) and Regenerate Shopping List (Module 14)

### Design Approach

**Key architectural decision:** Before ingredients reach this prompt, the Make.com aggregation step prefixes each recipe's ingredient list with `RECIPE: [recipe name]`. This gives Claude full traceability — it knows which recipe each ingredient comes from, enabling accurate attribution in the output.

Additionally, the selected recipe names for each meal type are passed in explicitly using `join(map(dinner_recipes; "recipe_name"); ", ")` so Claude can construct the meal plan summary header without guessing.

### Prompt Template (Weekly Agent — Module 26)

```
You are creating a consolidated shopping list from multiple recipes.

THIS WEEK'S MEAL PLAN:
Dinners: {join of dinner recipe names}
Lunches: {join of lunch recipe names}
Breakfasts: {join of breakfast recipe names}

INGREDIENTS FROM SELECTED RECIPES:
{aggregated_ingredients — each recipe prefixed with "RECIPE: [name]"}

ADDITIONAL SHOPPING LIST ITEMS: {shopping_list_additions}

YOUR TASK:
1. Combine all ingredients and additional items
2. Merge duplicate ingredients and sum their quantities
3. Organize by category - create categories dynamically based on both recipe ingredients AND shopping list additions
4. CRITICAL: Include EVERY item from "ADDITIONAL SHOPPING LIST ITEMS" section, even if it's the only item in its category
5. Format as a clean shopping list

COMBINATION RULES:
- Combine like ingredients (e.g., "2 cups flour" + "1 cup flour" = "3 cups flour")
- If units differ, list both (e.g., "2 lbs chicken breast, 1 rotisserie chicken")
- Round to practical quantities (e.g., 2.5 cups → 2½ cups)
- For items from "Always Include" or "Include this week", keep them even if duplicates

PANTRY STAPLES (don't worry about quantities):
- Salt, pepper, cooking oil, olive oil
- Basic spices (garlic powder, onion powder, etc.)
- Flour, sugar (unless recipe needs large amount like baking)
- Butter (unless recipe needs multiple sticks)

For pantry staples, just note "check pantry" instead of specific quantities unless the recipe requires an unusually large amount.

RECIPE ATTRIBUTION RULES:
- Each ingredient line must include which recipe(s) use it, in parentheses at the end
- If an ingredient is used by multiple recipes, list all recipe names separated by commas
- Items from ADDITIONAL SHOPPING LIST ITEMS do not need recipe attribution
- Format: "- Item, quantity (Recipe 1, Recipe 2)"

OUTPUT FORMAT:
Your output must follow this exact structure:

DINNERS: [comma separated dinner recipe names]
LUNCHES: [comma separated lunch recipe names]
BREAKFASTS: [comma separated breakfast recipe names]

Then a blank line, then the shopping list organized by category.

Example:
DINNERS: Chicken Tacos, Fajitas, Spaghetti
LUNCHES: Caesar Wrap, Tuna Sandwich
BREAKFASTS: Protein Smoothie, Eggs and Toast

**PRODUCE**
- Bananas, 6 (Protein Smoothie)
- Cilantro, 1 bunch (Chicken Tacos, Fajitas)
- Lettuce, 1 head (Caesar Wrap)

**DAIRY**
- Shredded cheese, 2 cups (Chicken Tacos, Fajitas)
- Eggs, 6 (Protein Scramble)

**HOUSEHOLD**
- Paper towels, 1 roll

Return ONLY the formatted output. No preamble, no explanation.
```

### Prompt Template (Regenerate Scenario — Module 14)

The regenerate prompt is identical in instructions, but the variable references differ because the regenerate scenario fetches dinners, lunches, and breakfasts in separate iterator chains (modules 7, 10, 13) rather than a merged iterator:

```
INGREDIENTS FROM SELECTED RECIPES:
{module_7_text — dinner ingredients}
{module_10_text — lunch ingredients}
{module_13_text — breakfast ingredients}

ADDITIONAL SHOPPING LIST ITEMS: {module_4_text}
```

Everything else — combination rules, pantry staples, attribution rules, output format — is identical to the weekly agent prompt.

### Example Output

```
DINNERS: Air Fryer Chicken Breast, Lemon Garlic Salmon with Orzo, Grilled Cheese and Homemade Tomato Soup, Sourdough / Homemade Pizza, Mini Meatloaves with Green Beans & Potatoes
LUNCHES: House Salad, Caesar Salad
BREAKFASTS: Bagels with Cream Cheese

**PRODUCE**
- Zucchini squash, 1 (Side of Roasted Veggies)
- Yellow squash, 1 (Side of Roasted Veggies)
- Red pepper, 1 (Side of Roasted Veggies)
- Lettuce, 1 head (House Salad)
- Garlic, 1 bulb (Lemon Garlic Salmon with Orzo)
- Lemon, 1 (Lemon Garlic Salmon with Orzo)
- Fresh spinach, 2 cups (Lemon Garlic Salmon with Orzo)
- Fresh green beans, 1 lb (Mini Meatloaves with Green Beans & Potatoes)
- Red or gold potatoes, 1 pack (Mini Meatloaves with Green Beans & Potatoes)

**MEAT & SEAFOOD**
- Boneless skinless chicken breasts, 1 lb (Air Fryer Chicken Breast)
- Salmon fillets, 4 (Lemon Garlic Salmon with Orzo)
- Ground beef, 1 lb (Mini Meatloaves with Green Beans & Potatoes)
- Pepperoni, 1 bag (Sourdough / Homemade Pizza)

**DAIRY**
- Milk, 1 bottle (Grilled Cheese and Homemade Tomato Soup)
- Butter - check pantry (Lemon Garlic Salmon with Orzo, Grilled Cheese and Homemade Tomato Soup)
- Feta cheese, ½ cup (Lemon Garlic Salmon with Orzo)
- Sliced cheese (Grilled Cheese and Homemade Tomato Soup)
- Mozzarella cheese, 4 cups (Sourdough / Homemade Pizza)
- Eggs, 18 total (Lemon Garlic Salmon with Orzo, Mini Meatloaves with Green Beans & Potatoes)
- Cream cheese, 1 tub (Bagels with Cream Cheese)

**PANTRY & CONDIMENTS**
- Smoked paprika - check pantry (Air Fryer Chicken Breast)
- Cornstarch (Air Fryer Chicken Breast)
- Salad dressing (House Salad)
- Tomato soup, 1 can (Grilled Cheese and Homemade Tomato Soup)
- Pizza sauce, 1 jar (Sourdough / Homemade Pizza)
- Worcestershire sauce (Mini Meatloaves with Green Beans & Potatoes)
- Italian seasoned bread crumbs, 1 cup (Mini Meatloaves with Green Beans & Potatoes)

**GRAINS & BREAD**
- Uncooked orzo pasta, 1 cup (Lemon Garlic Salmon with Orzo)
- Chicken broth, 2 cups (Lemon Garlic Salmon with Orzo)
- Sandwich bread, 1 loaf (Grilled Cheese and Homemade Tomato Soup)
- Pizza crust (Sourdough / Homemade Pizza)
- Bagels, 1 pack (Bagels with Cream Cheese)

**BEVERAGES**
- Apple juice, 1 bottle

**HOUSEHOLD**
- Paper towels, 1 roll
```

---

## Prompt Engineering Lessons Learned

### 1. Constraint Priority Hierarchy

**Early Version (Failed):**
```
Try to select recipes that:
- Haven't been used recently
- Match user preferences
- Hit health targets
- Vary protein sources
```

**Problem:** "Try to" is too weak. AI treated all constraints equally, leading to unexpected behavior.

**Improved Version:**
```
HARD CONSTRAINTS (MUST BE MET):
1. Exact meal counts
2. Meal type integrity

SOFT CONSTRAINTS (PRIORITIZE, BUT CAN BE FLEXIBLE):
3. Recency
4. User preferences
5. Health score
...
```

**Lesson:** Explicit priority hierarchy with "HARD" vs "SOFT" language works better than "try to" or "prefer".

### 2. Negative Instructions Are Powerful

**Weak:**
```
Select recipes that were planned more than 2-3 weeks ago
```

**Strong:**
```
DO NOT select recipes where Last Planned Date is within the last 14 days
```

**Lesson:** Negative constraints ("DO NOT") are clearer than positive ones ("Select recipes that...").

### 3. Examples Prevent Edge Cases

**Without Examples:**
```
Never select multiple sides with the same Side Type
```

**Result:** AI still occasionally violated this rule.

**With Examples:**
```
CRITICAL DIVERSITY RULE: Never select multiple sides with the same Side Type
  - Example: If you select "Rice" (Side Type = Starch), you cannot also select
    "Mashed Potatoes" (Side Type = Starch)
  - You must balance: Starch + Vegetable, or Starch + Salad, etc.
```

**Result:** Violations dropped to near-zero.

**Lesson:** Concrete examples are worth 1000 words of abstract instruction.

### 4. Structured Output Requires Exact Schema

**Weak:**
```
Return JSON with recipe IDs and reasoning
```

**Strong:**
```
Return a JSON object with this EXACT structure:
{
  "success": true,
  "dinner_recipes": [
    {"recipe_id": "rec123abc", "recipe_name": "Chicken Tacos", "notes": "Brief reason"},
    ...
  ],
  ...
}
```

**Lesson:** Show exact schema with example values. "EXACT structure" language helps.

### 5. Multiple Reminders for Critical Rules

Notice how "Meal Type Integrity" appears multiple times in Prompt 1:
1. In HARD CONSTRAINTS section
2. In OUTPUT FORMAT section
3. In CRITICAL INSTRUCTION notes

**Lesson:** Critical rules need reinforcement in multiple places. Repetition works.

### 6. Upstream Data Preparation Enables Downstream Intelligence

The recipe attribution in the shopping list output is not magic — it works because Make.com prepares the data correctly before Claude sees it. Each recipe's ingredient list is prefixed with `RECIPE: [name]` in the text aggregator module. Claude then has the context it needs to attribute each ingredient accurately.

**Lesson:** What you feed the model matters as much as how you prompt it. Good data preparation upstream reduces prompt complexity downstream.

### 7. Traceability Builds Trust

Adding recipe attribution transformed the shopping list from "output to accept" into "output to verify." Users can now see exactly where every ingredient came from — catching hallucinations, making informed substitutions, and understanding why something is on the list.

This was a small prompt change (adding attribution rules and the `RECIPE: [name]` prefix upstream) with a large usability impact.

**Lesson:** Explainability in AI output isn't just a nice-to-have. It's what turns AI output into something people actually rely on.

### 8. Natural Language Input Works

The "Recipe Search Preferences" field accepts natural language:
- "want tacos"
- "use up ground beef"
- "need something for grilling Wednesday"

**Why it works:** Claude is optimized for natural language understanding. Fighting this with structured formats is counterproductive.

**Lesson:** Let users write like humans. Use AI's strength rather than requiring structured input.

---

## API Integration Details

### Make.com HTTP Module Configuration

**Method:** POST

**URL:** `https://api.anthropic.com/v1/messages`

**Headers:**
```
x-api-key: [API key stored in Make.com]
anthropic-version: 2023-06-01
content-type: application/json
```

**Body:**
```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 4000,
  "messages": [
    {
      "role": "user",
      "content": "[Prompt template filled with Make.com variable mappings]"
    }
  ]
}
```

### Response Parsing

Claude returns:
```json
{
  "id": "msg_...",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "{\"success\": true, \"dinner_recipes\": [...], ...}"
    }
  ],
  "model": "claude-sonnet-4-20250514"
}
```

**Make.com Parsing:**
1. Extract text: `{{response.data.content[1].text}}` (Make.com uses 1-indexed arrays)
2. For Prompt 1 (recipe selection): Use "Parse JSON" module with pre-defined data structure
3. For Prompt 2 (shopping list): Text is used directly — stored in Airtable and written to Google Doc

### Error Handling

**Success Check (Prompt 1 only):**
- Route on `{{json_parser.success}}` == true / false
- False route: Log error message, skip Weekly Plan creation

**Validation:**
- Prompt 1 returns `success: false` with explanation if constraints cannot be met
- Prompt 2 has no failure mode — always returns formatted text

---

## Cost Analysis

**Per Weekly Run:**
- Recipe Selection (Prompt 1): ~1,500 input tokens, ~800 output tokens
- Shopping List (Prompt 2): ~1,400 input tokens, ~1,500 output tokens
- Total: ~5,200 tokens per week

**Cost at Sonnet 4 pricing:**
- Input: $3 per million tokens
- Output: $15 per million tokens
- Weekly cost: ~$0.027 (less than 3 cents)
- Annual cost: ~$1.40

**Conclusion:** API costs are negligible for this use case. The value (40+ min/week saved) far outweighs cost.

---

## Future Prompt Enhancements

**Potential Improvements:**

1. **Few-shot examples** - Include 2-3 examples of good recipe selections in Prompt 1
2. **Chain-of-thought** - Ask Claude to reason through constraints before selecting
3. **Feedback loop** - Incorporate user's manual adjustments to learn preferences over time
4. **Conversational refinement** - Allow user to refine selections through dialogue before committing
5. **Macro tracking** - Extend shopping list prompt to include approximate nutritional totals per category

**Not Implemented Yet:** These would improve quality but add complexity. Current ~98% success rate is acceptable for now.
