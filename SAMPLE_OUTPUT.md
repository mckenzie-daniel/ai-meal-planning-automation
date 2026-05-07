# Sample Weekly Output

This is an example of what the automation system produces each week.

---

## Week of May 7, 2026

### Selected Recipes

**Dinners:**
1. Air Fryer Chicken Breast (+ Side of Roasted Veggies)
2. Lemon Garlic Salmon with Orzo
3. Grilled Cheese and Homemade Tomato Soup
4. Sourdough / Homemade Pizza
5. Mini Meatloaves with Green Beans & Potatoes
6. Costa Rican Casados

**Lunches:**
1. House Salad
2. Caesar Salad
3. Pizza Rollup
4. Egg Salad Sandwiches

**Breakfasts:**
1. Bagels with Cream Cheese

---

### Claude's Reasoning

"Selected recipes balancing protein variety (chicken, fish, beef, eggs) with health targets. All recipes outside the 14-day recency window. Limited high-effort meals to one (Homemade Pizza). Side dishes selected for nutritional balance without Side Type duplication. Honored pantry surplus — used eggs across multiple recipes (salmon, meatloaves, egg salad, casados). Costa Rican Casados adds international variety and uses pantry staples."

---

### Shopping List (Auto-Generated Google Doc)

This shopping list is automatically generated and written to a Google Doc linked directly from the Airtable Weekly Plans record. The format includes a meal plan summary header followed by a categorized, attributed ingredient list.

```
DINNERS: Air Fryer Chicken Breast, Side of Roasted Veggies, Lemon Garlic Salmon with Orzo, Grilled Cheese and Homemade Tomato Soup, Sourdough / Homemade Pizza, Mini Meatloaves with Green Beans & Potatoes, Costa Rican Casados
LUNCHES: House Salad, Caeser Salad, Pizza Rollup, Egg Salad Sandwiches
BREAKFASTS: Bagels with Cream Cheese

**PRODUCE**
- Bananas, 6
- Apples, 4
- Zucchini squash, 1 (Side of Roasted Veggies)
- Yellow squash, 1 (Side of Roasted Veggies)
- Red pepper, 1 (Side of Roasted Veggies)
- Lettuce, 1 head (House Salad)
- Carrots (House Salad)
- Tomato (House Salad)
- Garlic, 1 bulb (Lemon Garlic Salmon with Orzo)
- Lemon, 1 (Lemon Garlic Salmon with Orzo)
- Fresh spinach, 2 cups (Lemon Garlic Salmon with Orzo)
- Fresh green beans, 1 lb (Mini Meatloaves with Green Beans & Potatoes)
- Red or gold potatoes, 1 pack (Mini Meatloaves with Green Beans & Potatoes)
- White onion, 1 (Mini Meatloaves with Green Beans & Potatoes)

**MEAT & SEAFOOD**
- Boneless skinless chicken breasts, 1 lb (Air Fryer Chicken Breast)
- Salmon fillets, 4 (Lemon Garlic Salmon with Orzo)
- Ground beef, 1 lb (Mini Meatloaves with Green Beans & Potatoes)
- Pepperoni, 1 bag (Sourdough / Homemade Pizza, Pizza Rollup)

**DAIRY**
- Milk, 1 bottle (Grilled Cheese and Homemade Tomato Soup)
- Butter - check pantry (Lemon Garlic Salmon with Orzo, Grilled Cheese and Homemade Tomato Soup, Costa Rican Casados)
- Feta cheese, ½ cup (Lemon Garlic Salmon with Orzo)
- Sliced cheese (Grilled Cheese and Homemade Tomato Soup)
- Mozzarella cheese, 4 cups (Sourdough / Homemade Pizza)
- String cheese (Pizza Rollup)
- Eggs, 18 total (Lemon Garlic Salmon with Orzo, Mini Meatloaves with Green Beans & Potatoes, Egg Salad Sandwiches, Costa Rican Casados)
- Cream cheese, 1 tub (Bagels with Cream Cheese)

**PANTRY & CONDIMENTS**
- Paprika - check pantry (Air Fryer Chicken Breast, Mini Meatloaves with Green Beans & Potatoes)
- Smoked paprika - check pantry (Air Fryer Chicken Breast)
- Cornstarch (Air Fryer Chicken Breast)
- Salad dressing (House Salad)
- Croutons (House Salad)
- Tomato soup, 1 can (Grilled Cheese and Homemade Tomato Soup)
- Pizza sauce, 1 jar (Sourdough / Homemade Pizza, Pizza Rollup)
- Worcestershire sauce (Mini Meatloaves with Green Beans & Potatoes)
- Italian seasoned bread crumbs, 1 cup (Mini Meatloaves with Green Beans & Potatoes)
- Ketchup (Mini Meatloaves with Green Beans & Potatoes)
- Mayo (Egg Salad Sandwiches)
- Mustard (Egg Salad Sandwiches)
- Sweet relish (Egg Salad Sandwiches)
- Black beans, 1 can (Costa Rican Casados)
- White rice, 1 cup (Costa Rican Casados)
- Salsa Lizano (Costa Rican Casados)

**GRAINS & BREAD**
- Uncooked orzo pasta, 1 cup (Lemon Garlic Salmon with Orzo)
- Chicken or vegetable broth, 2 cups (Lemon Garlic Salmon with Orzo)
- Sandwich bread, 1 loaf (Grilled Cheese and Homemade Tomato Soup, Costa Rican Casados)
- Pizza crust (Sourdough / Homemade Pizza)
- Tortilla (Pizza Rollup)
- Bagels, 1 pack (Bagels with Cream Cheese)

**BEVERAGES**
- Apple juice, 1 bottle

**PREPARED FOODS**
- Caesar salad mix, 1 package (Caeser Salad)
- Crackers - optional (Grilled Cheese and Homemade Tomato Soup)
```

---

## How This Was Generated

### Step 1: User Configuration (One-Time Setup)

**User Preferences Table:**
- Number of Dinners: 5-7 (varies by week)
- Number of Lunches: 2-4
- Number of Breakfasts: 1-2
- Target Health Score: 6.0
- Max High Effort Meals: 2

**This Week's Preferences (Updated Weekly):**
- Pantry Surplus: "Have extra eggs"
- Recipe Search Preferences: "Something easy for Wednesday night"

**Shopping List Additions:**
- Apple juice (Include this week ✓)
- Apples (Always include ✓)
- Bananas (Always include ✓)

### Step 2: Automation Runs (Sunday 9am MST)

**Make.com Scenario Execution:**

1. **Retrieve Data** (Modules 2-4)
   - Fetched user preferences from Airtable
   - Retrieved 70+ recipes from database (filtered to "Ready for AI" and "Available for Selection")
   - Got shopping list additions

2. **Aggregate Recipe Data** (Modules 7-8)
   - Combined all recipe metadata into structured text
   - Prepared shopping list additions text
   - Both fed into Claude API call

3. **Claude API Call #1 — Recipe Selection** (Module 5)
   - **Input:** User preferences + recipe database + current date + shopping list additions
   - **Processing time:** ~15 seconds
   - **Output:** JSON with recipe IDs, recipe names, and reasoning

4. **Parse JSON Response** (Module 16)
   - Extracted `dinner_recipes` array
   - Extracted `lunch_recipes` array
   - Extracted `breakfast_recipes` array
   - Extracted `health_score` and `reasoning`

5. **Retrieve Ingredients** (Modules 23-25)
   - Iterator merges all selected recipes into one array
   - For each recipe: fetch full record from Airtable (including Ingredient List)
   - Text Aggregator prefixes each recipe's ingredients with `RECIPE: [name]`
   - Result: single text block with full ingredient context per recipe

6. **Claude API Call #2 — Shopping List** (Module 26)
   - **Input:** Meal plan header data (recipe names by type) + attributed ingredient text + shopping list additions
   - **Processing time:** ~17 seconds
   - **Output:** Meal summary header + categorized, attributed shopping list

7. **Create Weekly Plan in Airtable** (Module 27)
   - New record in Weekly Plans table
   - Linked recipe IDs for dinners, lunches, breakfasts
   - Stored full shopping list text (with attribution)
   - Saved Claude's reasoning message

8. **Generate Google Doc** (Module 43)
   - Creates new Google Doc: `Shopping List - Week of MM/DD/YYYY`
   - Content: full Claude output from Step 6
   - Saved to designated Google Drive folder

9. **Update Airtable with Google Doc URL** (Module 44)
   - Writes `webViewLink` from Google Doc back to Weekly Plans record (`Ingredient Doc URL` field)
   - User can now open the doc directly from Airtable

10. **Update Recipe Metadata** (Modules 28-42)
    - Three parallel iterator flows (dinners, lunches, breakfasts)
    - Updates `Last Planned Date` for each selected recipe
    - Enables 14-day recency tracking for next week's run

11. **Cleanup** (Modules 32-34)
    - Resets `Include this week?` to false for one-off shopping additions
    - Leaves `Always Include?` items unchanged

**Total Execution Time:** ~60 seconds

### Step 3: User Review (Manual)

**User opens Airtable:**
- Reviews selected recipes for the week
- Reads Claude's reasoning
- Clicks `Ingredient Doc URL` to open Google Doc

**User opens Google Doc:**
- Sees meal plan summary at top (DINNERS / LUNCHES / BREAKFASTS)
- Reviews attributed shopping list — can see exactly which recipe uses each ingredient
- Selects all, applies Google Docs checkbox list format for use while shopping

**Optional adjustments:**
- Swap a recipe manually in Airtable
- Click "Regenerate Shopping List" button — triggers Regenerate scenario, creates new Google Doc, updates URL in Airtable

**Typical outcome:**
- 80% of weeks: No changes needed, use plan as-is
- 15% of weeks: Swap 1 recipe, regenerate list
- 5% of weeks: Significant changes (unexpected schedule change)

---

## Why Recipe Attribution Matters

Every ingredient in the shopping list shows which recipe(s) use it:

```
- Eggs, 18 total (Lemon Garlic Salmon with Orzo, Mini Meatloaves with Green Beans & Potatoes, Egg Salad Sandwiches, Costa Rican Casados)
```

**This enables three things:**

1. **Sanity checking** — If you see an unexpected ingredient, you can immediately trace which recipe it came from and verify it's correct. Hallucinations become obvious.

2. **Informed substitutions** — If you want to skip cilantro, you can see it's only used in one recipe. If it's used in three recipes, skipping it has bigger consequences.

3. **Quantity confidence** — Seeing "18 eggs across 4 recipes" is more trustworthy than just "18 eggs." You understand why the quantity is what it is.

---

## Data Tracking Over Time

### Recipe Variety Tracking

**Last Planned Dates (Sample):**

| Recipe Name | Last Planned Date | Days Since |
|---|---|---|
| Air Fryer Chicken Breast | 2026-05-07 | 0 days |
| Lemon Garlic Salmon with Orzo | 2026-05-07 | 0 days |
| Mini Meatloaves | 2026-05-07 | 0 days |
| Grilled Cheese and Tomato Soup | 2026-05-07 | 0 days |
| Chicken Tacos | 2026-04-28 | 9 days |
| Tangy Beef Stew | 2026-04-21 | 16 days |
| Spaghetti | 2026-04-14 | 23 days |
| Lasagna | 2026-04-07 | 30 days |

**Recency Rule in Action:**
- Recipes with Last Planned Date within 14 days are avoided
- Exception: If user explicitly requests in Recipe Search Preferences
- Ensures natural rotation through recipe database

### Health Score Tracking

**Weekly Health Scores:**

| Week Starting | Target | Actual | Variance |
|---|---|---|---|
| 2026-05-07 | 6.0 | 6.2 | +0.2 |
| 2026-04-28 | 6.0 | 5.9 | -0.1 |
| 2026-04-21 | 6.0 | 6.4 | +0.4 |
| 2026-04-14 | 6.0 | 6.1 | +0.1 |
| 2026-04-07 | 6.0 | 5.8 | -0.2 |

**Success Rate:** 95% of weeks within ±1 point of target

---

## Edge Cases Handled

### Edge Case 1: Not Enough Recent Recipes

**Scenario:** Limited lunch recipes in database, need 4 per week with 14-day recency

**Solution:** Claude deprioritizes (but does not ignore) recency for lunch when database size is insufficient. Explains the trade-off in reasoning field.

### Edge Case 2: User Requests Conflicting Constraints

**User Input:**
- Recipe Search Preferences: "Want quick meals, nothing over 30 minutes"
- Target Health Score: 9.0 (very high)

**Problem:** High health score recipes typically require more prep time.

**Claude's Response:**
```json
{
  "success": true,
  "health_score": 8.2,
  "reasoning": "Selected quickest high-health recipes available (salads, grilled proteins). Achieved 8.2 health score vs 9.0 target due to constraint conflict between speed and health. Prioritized user's explicit request for quick meals."
}
```

**Outcome:** System explains trade-off made, prioritizes explicit user request.

### Edge Case 3: Pantry Surplus Dominates

**User Input:**
- Pantry Surplus: "Have 5 lbs ground beef to use up"

**Risk:** Could select ground beef recipes every night (poor protein variety)

**Claude's Behavior:**
- Selected 3 ground beef recipes across the week
- Balanced with 2 other proteins
- Reasoning: "Used ground beef surplus in 3/5 dinners while maintaining protein variety"

**Outcome:** Honors user request without violating variety constraints.

### Edge Case 4: Manual Recipe Swap

**User Action:**
- Swaps one dinner recipe manually in Airtable
- Clicks "Regenerate Shopping List"

**System Behavior:**
- Regenerate scenario re-fetches all linked recipes from the Weekly Plan record
- Calls Claude with updated ingredient list
- Creates new Google Doc with updated date suffix in title
- Updates `Ingredient Doc URL` field in Airtable with new link

**Outcome:** User always has a current, accurate Google Doc after any change.

---

## Success Metrics

**From 6+ Months of Production Use:**

| Metric | Target | Actual | Status |
|---|---|---|---|
| Meal count accuracy | 100% | 100% | ✓ |
| Meal type integrity | 100% | 100% | ✓ |
| Recency rule compliance | 90% | 97% | ✓ |
| Health score within target | 90% | 95% | ✓ |
| No manual intervention needed | 70% | 80% | ✓ |
| Google Doc generated automatically | 100% | 100% | ✓ |
| Overall success rate | 95% | 98% | ✓ |
| Time saved per week | 30 min | 40+ min | ✓ |

---

## What This Demonstrates

**For Technical Audiences:**
- Multi-constraint optimization using LLMs
- Production-grade automation with error handling
- Ingredient traceability through upstream data preparation
- Iterator/aggregator patterns in no-code workflow tools
- Google Docs API integration via Make.com

**For Non-Technical Audiences:**
- AI solving real daily problems
- Automation that saves measurable time every week
- A system that explains its own reasoning
- Practical, production application of AI agents

**For Hiring Managers:**
- Hands-on AI implementation experience
- Systems thinking and architecture skills
- Ability to iterate from "working" to "production-ready"
- Focus on explainability and user trust in AI outputs
- Builder mentality — solving real problems by building real solutions
