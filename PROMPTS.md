# Claude API Prompts

This document contains the actual prompts used in the meal planning automation system. These prompts demonstrate multi-constraint optimization and structured output generation.

---

## Prompt 1: Recipe Selection

**Purpose:** Select meals for the week based on multiple competing constraints

**Model:** claude-sonnet-4-20250514

**Temperature:** Default (~1.0) - Non-determinism desired for variety

**Max Tokens:** 4000

### System Prompt

```
You are an expert meal planning assistant. Your job is to select recipes for the week based on user preferences and constraints.

You will be given:
1. User preferences (number of meals, health targets, effort limits, weekly notes)
2. A database of available recipes with metadata
3. Current date (for calculating recency)
4. Shopping list additions

You must select the exact number of meals requested while balancing multiple constraints.

CRITICAL: Return ONLY valid JSON. No preamble, no explanation outside the JSON structure.
```

### User Prompt Template

```
Today's date: {current_date}

USER PREFERENCES:
- Number of Dinners to Plan: {num_dinners}
- Number of Lunches to Plan: {num_lunches}
- Number of Breakfasts to Plan: {num_breakfasts}
- Target Weekly Health Score: {target_health_score}
- Max High Effort Meals: {max_high_effort}
- Pantry Surplus/Preferences: {pantry_notes}
- Recipe Search Preferences: {weekly_preferences}

AVAILABLE RECIPES:
{aggregated_recipe_data}

SHOPPING LIST ADDITIONS:
{shopping_list_items}

---

CONSTRAINTS (in priority order):

HARD CONSTRAINTS (Non-negotiable):
1. Select EXACTLY {num_dinners} dinner recipes, {num_lunches} lunch recipes, {num_breakfasts} breakfast recipes
2. Meal Type Integrity: Lunch recipes ONLY go in lunch_recipes array. Dinner recipes ONLY go in dinner_recipes array. Breakfast recipes ONLY go in breakfast_recipes array. NEVER mix meal types.

SOFT CONSTRAINTS (Strongly preferred, but flexible):
3. Recency: DO NOT select recipes where Last Planned Date is within the last 14 days, UNLESS the user explicitly requests a specific recipe in Recipe Search Preferences
4. User's Recipe Search Preferences: Honor specific requests when possible (e.g., "want tacos", "use up ground beef")
5. Health Score: Target average health score of {target_health_score} ± 1 point across all selected recipes
6. Effort Level: Try to limit high-effort meals to {max_high_effort} maximum
7. Protein Variety: Avoid same protein 3+ days in a row
8. Seasonal Preference: Lowest priority - can be ignored if conflicts with other constraints

SIDE DISH RULES:
- When a main dish has "Needs Sides = true", you MUST select side dishes from the "Suggested Sides" list
- CRITICAL DIVERSITY RULE: Never select multiple sides with the same Side Type for a single meal
  - Example: If you select "Rice" (Side Type = Starch), you cannot also select "Mashed Potatoes" (Side Type = Starch)
  - You must balance: Starch + Vegetable, or Starch + Salad, etc.
- The number of sides to select is specified in "Number of Sides Needed"

OUTPUT FORMAT:
Return a JSON object with this EXACT structure:

{
  "success": true,
  "dinner_recipes": [
    {"recipe_id": "rec123abc", "notes": "Brief reason for selection"},
    {"recipe_id": "rec456def", "notes": "Brief reason for selection"},
    // ... exactly {num_dinners} items (includes main dishes AND their sides)
  ],
  "lunch_recipes": [
    {"recipe_id": "rec789ghi", "notes": "Brief reason for selection"},
    // ... exactly {num_lunches} items
  ],
  "breakfast_recipes": [
    {"recipe_id": "recXYZ123", "notes": "Brief reason for selection"},
    // ... exactly {num_breakfasts} items
  ],
  "health_score": 7.2,
  "reasoning": "2-3 sentence explanation of overall selections, mentioning any constraints that were relaxed or priorities made"
}

IMPORTANT:
- If you cannot meet the constraints (e.g., not enough recipes available), set "success": false and explain in "reasoning"
- Ensure all recipe_id values exist in the provided recipe database
- Double-check meal type integrity - lunches in lunch_recipes ONLY, dinners in dinner_recipes ONLY
- Double-check you have EXACTLY the right number of meals for each type
```

### Example Response

```json
{
  "success": true,
  "dinner_recipes": [
    {"recipe_id": "rec1A2B3C", "notes": "Korean Beef Bowl - high protein, haven't had in 3 weeks"},
    {"recipe_id": "rec4D5E6F", "notes": "Steamed Broccoli - pairs well, vegetable side"},
    {"recipe_id": "rec7G8H9I", "notes": "Grilled Chicken Tacos - user requested tacos, protein variety"},
    {"recipe_id": "recJ1K2L3", "notes": "Mexican Rice - starch side for tacos"},
    {"recipe_id": "recM4N5O6", "notes": "Baked Salmon - fish for protein variety, high health score"},
    {"recipe_id": "recP7Q8R9", "notes": "Roasted Vegetables - vegetable side, complements salmon"},
    {"recipe_id": "recS1T2U3", "notes": "Spaghetti Bolognese - medium effort, family favorite"},
    {"recipe_id": "recV4W5X6", "notes": "Pork Chops - protein variety, moderate health score"},
    {"recipe_id": "recY7Z8A9", "notes": "Mashed Potatoes - starch side for pork chops"}
  ],
  "lunch_recipes": [
    {"recipe_id": "recB1C2D3", "notes": "Turkey Sandwich - low effort, good for quick lunch"},
    {"recipe_id": "recE4F5G6", "notes": "Chicken Salad - high health score, uses leftover chicken"}
  ],
  "breakfast_recipes": [
    {"recipe_id": "recH7I8J9", "notes": "Scrambled Eggs - quick, high protein"},
    {"recipe_id": "recK1L2M3", "notes": "Oatmeal - high health score, low effort"}
  ],
  "health_score": 7.1,
  "reasoning": "Selected recipes balancing protein variety (beef, chicken, fish, pork) with health targets. Honored user's request for tacos. All recipes outside 14-day recency window except for one family favorite (spaghetti at 10 days). Limited to 1 high-effort meal (spaghetti). Side dishes provide starch/vegetable balance without duplication."
}
```

---

## Prompt 2: Shopping List Generation

**Purpose:** Consolidate ingredients from all selected recipes into organized grocery list

**Model:** claude-sonnet-4-20250514

**Temperature:** Default

**Max Tokens:** 2000

### System Prompt

```
You are a grocery list consolidation expert. Your job is to take ingredients from multiple recipes and create a well-organized shopping list.

You will merge duplicate ingredients, handle unit conversions intelligently, and organize by grocery store category.

Return ONLY the formatted shopping list text. No preamble, no JSON, just the list.
```

### User Prompt Template

```
INGREDIENTS FROM SELECTED RECIPES:
{aggregated_ingredients_from_all_recipes}

ADDITIONAL SHOPPING LIST ITEMS:
{shopping_list_additions}

---

INSTRUCTIONS:

1. MERGE DUPLICATE INGREDIENTS:
   - Combine quantities when units match (e.g., "2 cups flour" + "1 cup flour" = "3 cups flour")
   - Handle unit differences intelligently (e.g., "1 lb tomatoes" + "8 oz tomatoes" = "1.5 lbs tomatoes")
   - If units don't convert easily, list separately with note (e.g., "2 cups diced tomatoes" AND "1 lb fresh tomatoes")

2. ORGANIZE BY CATEGORY:
   Use these categories in this order:
   - PRODUCE
   - MEAT & SEAFOOD
   - DAIRY & EGGS
   - PANTRY & DRY GOODS
   - FROZEN
   - BREAD & BAKERY
   - CONDIMENTS & SAUCES
   - SPICES & SEASONINGS
   - BEVERAGES
   - HOUSEHOLD (if applicable)
   - OTHER

3. FORMATTING:
   - Each category as ALL CAPS header
   - Items as bullet points with "•" character
   - Include quantities and units
   - Be specific (e.g., "2 lbs tomatoes (Roma or plum)" not just "tomatoes")

4. STAPLES NOTE:
   - For common staples (salt, pepper, oil, flour, sugar), add "(check pantry)" unless recipe needs large quantity
   - Large quantity example: "5 cups flour" → list without pantry note

5. INCLUDE ALL ITEMS:
   - All ingredients from recipes
   - All items from "ADDITIONAL SHOPPING LIST ITEMS" section

OUTPUT FORMAT EXAMPLE:

PRODUCE
• 2 lbs tomatoes (Roma or plum)
• 1 bunch fresh basil
• 3 bell peppers (red)
• 2 yellow onions
• 1 lb baby carrots

MEAT & SEAFOOD
• 2 lbs ground beef (80/20)
• 1.5 lbs chicken breast
• 1 lb salmon fillet

DAIRY & EGGS
• 1 gallon milk (check pantry)
• 2 cups shredded cheddar cheese
• 1 dozen eggs (check pantry)
• 1 stick unsalted butter

PANTRY & DRY GOODS
• 3 cups flour (check pantry)
• 1 box spaghetti (16 oz)
• 2 cups white rice

... (continue for all categories)
```

### Example Response

```
PRODUCE
• 3 lbs tomatoes (Roma or plum)
• 2 bunches fresh basil
• 5 bell peppers (2 red, 2 green, 1 yellow)
• 3 yellow onions
• 2 lbs baby carrots
• 1 head broccoli
• 2 lbs mixed salad greens
• 3 limes

MEAT & SEAFOOD
• 2 lbs ground beef (80/20)
• 2 lbs chicken breast
• 1 lb salmon fillet
• 1 lb pork chops (bone-in)

DAIRY & EGGS
• 1 gallon milk (check pantry)
• 3 cups shredded cheddar cheese
• 1 cup sour cream
• 2 dozen eggs
• 2 sticks unsalted butter

PANTRY & DRY GOODS
• 5 cups all-purpose flour
• 2 boxes spaghetti (16 oz each)
• 3 cups white rice
• 2 cans black beans (15 oz)
• 1 can diced tomatoes (28 oz)
• 2 cups chicken broth

CONDIMENTS & SAUCES
• 1 bottle soy sauce (check pantry)
• 1 jar marinara sauce (24 oz)
• 2 tablespoons olive oil (check pantry)
• 1 bottle fish sauce

SPICES & SEASONINGS
• Cumin (check pantry)
• Paprika (check pantry)
• Garlic powder (check pantry)
• Fresh garlic (1 head)
• Fresh ginger (2 inch piece)

BREAD & BAKERY
• 1 loaf whole wheat bread
• 8 flour tortillas (burrito size)

BEVERAGES
• Orange juice (1/2 gallon)

HOUSEHOLD
• Paper towels (from additional items list)
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
HARD CONSTRAINTS (Non-negotiable):
1. Exact meal counts
2. Meal type integrity

SOFT CONSTRAINTS (Strongly preferred, but flexible):
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
  - Example: If you select "Rice" (Side Type = Starch), you cannot also select "Mashed Potatoes" (Side Type = Starch)
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
    {"recipe_id": "rec123abc", "notes": "Brief reason for selection"},
    ...
  ],
  ...
}
```

**Lesson:** Show exact schema with example values. "EXACT structure" language helps.

### 5. Multiple Reminders for Critical Rules

Notice how "Meal Type Integrity" appears:
1. In HARD CONSTRAINTS section
2. In OUTPUT FORMAT section ("lunches in lunch_recipes ONLY")
3. In IMPORTANT notes ("Double-check meal type integrity")

**Lesson:** Critical rules need reinforcement in multiple places. Repetition works.

### 6. Natural Language Input Works

The "Recipe Search Preferences" field accepts natural language:
- "want tacos"
- "use up ground beef"
- "need something for grilling Wednesday"

**Why it works:** Claude is optimized for natural language understanding. Fighting this with structured formats is counterproductive.

**Lesson:** Let users write like humans. Use AI's strength (NLU) rather than requiring structured input.

---

## API Integration Details

### Make.com HTTP Module Configuration

**Method:** POST

**URL:** `https://api.anthropic.com/v1/messages`

**Headers:**
```
x-api-key: [API key from Make.com variable]
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
      "content": "[Prompt template filled with Airtable data]"
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
  "model": "claude-sonnet-4-20250514",
  ...
}
```

**Make.com Parsing:**
1. Extract: `{{response.content[1].text}}` (Make.com uses 1-indexed arrays)
2. Use "Parse JSON" module with pre-defined data structure
3. Maps to variables: `{{json_parser.dinner_recipes}}`, `{{json_parser.reasoning}}`, etc.

### Error Handling

**Success Check:**
```javascript
// In Make.com router
if ({{json_parser.success}} == false) {
  // Send error notification
  // Log reasoning message
  // Don't create Weekly Plan
}
```

**Validation:**
- Check array lengths match expected counts
- Verify all recipe_ids exist in Airtable
- Validate JSON structure before processing

---

## Cost Analysis

**Per Weekly Run:**
- Recipe Selection API call: ~1500 input tokens, ~800 output tokens
- Shopping List API call: ~800 input tokens, ~400 output tokens
- Total: ~3500 tokens per week

**Cost at Sonnet 4 pricing:**
- Input: $3 per million tokens
- Output: $15 per million tokens
- Weekly cost: ~$0.017 (less than 2 cents)
- Annual cost: ~$0.88

**Conclusion:** API costs are negligible for this use case. The value (40 min/week saved) far outweighs cost.

---

## Future Prompt Enhancements

**Potential Improvements:**

1. **Few-shot examples** - Include 2-3 examples of good recipe selections
2. **Chain-of-thought** - Ask Claude to think through constraints before selecting
3. **Structured reasoning** - Request specific format for reasoning (constraints satisfied, constraints relaxed, trade-offs made)
4. **Feedback loop** - Incorporate user's manual adjustments to learn preferences over time
5. **Conversational refinement** - Allow user to refine selections through dialogue before committing

**Not Implemented Yet:** These would improve quality but add complexity. Current ~98% success rate is acceptable for now.
