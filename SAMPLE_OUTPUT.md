# Sample Weekly Output

This is an example of what the automation system produces each week.

---

## Week of January 13, 2026

### Selected Recipes

**Dinners:**
1. Korean Beef Bowl (+ Steamed Broccoli)
2. Grilled Chicken Tacos (+ Mexican Rice)
3. Baked Salmon (+ Roasted Vegetables)
4. Spaghetti Bolognese
5. Pork Chops (+ Mashed Potatoes + Garden Salad)

**Lunches:**
1. Turkey Sandwich
2. Chicken Salad

**Breakfasts:**
1. Scrambled Eggs with Toast
2. Oatmeal with Berries

---

### Claude's Reasoning

"Selected recipes balancing protein variety (beef, chicken, fish, pork, turkey) with health targets averaging 7.1 across all meals. Honored user's request for tacos this week by including Grilled Chicken Tacos. All recipes outside 14-day recency window except for Spaghetti Bolognese (family favorite, last made 10 days ago - within acceptable range given user preference). Limited to 1 high-effort meal (Spaghetti). Side dishes provide starch/vegetable balance without Side Type duplication. Pork Chops paired with both starch (Mashed Potatoes) and salad for nutritional balance."

---

### Shopping List

```
PRODUCE
• 3 lbs tomatoes (Roma or plum)
• 2 bunches fresh basil
• 5 bell peppers (2 red, 2 green, 1 yellow)
• 3 yellow onions
• 2 lbs baby carrots
• 1 head broccoli
• 2 lbs mixed salad greens
• 1 head romaine lettuce
• 3 limes
• 2 cups fresh berries (strawberries or blueberries)
• 4 cloves garlic
• 2 inch piece fresh ginger
• 1 bunch green onions

MEAT & SEAFOOD
• 2 lbs ground beef (80/20)
• 2 lbs chicken breast
• 1 lb salmon fillet (fresh, skin-on)
• 4 pork chops (bone-in, 1 inch thick)
• 1 lb deli turkey breast (sliced)

DAIRY & EGGS
• 1 gallon milk (check pantry)
• 3 cups shredded cheddar cheese
• 1 cup sour cream
• 2 dozen eggs
• 2 sticks unsalted butter
• 1 cup plain Greek yogurt

PANTRY & DRY GOODS
• 5 cups all-purpose flour
• 2 boxes spaghetti (16 oz each)
• 3 cups white rice
• 2 cups rolled oats
• 2 cans black beans (15 oz)
• 1 can diced tomatoes (28 oz)
• 2 cups chicken broth
• 1 lb bag brown sugar

CONDIMENTS & SAUCES
• 1 bottle soy sauce (check pantry)
• 1 jar marinara sauce (24 oz)
• 4 tablespoons olive oil (check pantry)
• 1 bottle fish sauce
• Mayonnaise (check pantry)
• Dijon mustard (check pantry)

SPICES & SEASONINGS
• Cumin (check pantry)
• Paprika (check pantry)
• Garlic powder (check pantry)
• Italian seasoning (check pantry)
• Red pepper flakes (check pantry)
• Salt (check pantry)
• Black pepper (check pantry)

BREAD & BAKERY
• 1 loaf whole wheat bread
• 8 flour tortillas (burrito size)

FROZEN
• 2 lbs frozen mixed vegetables (for quick sides)

BEVERAGES
• Orange juice (1/2 gallon)

HOUSEHOLD
• Paper towels (from weekly additions)
• Dish soap (from weekly additions)
```

---

## How This Was Generated

### Step 1: User Configuration (One-Time Setup)

**User Preferences Table:**
- Number of Dinners: 5
- Number of Lunches: 2  
- Number of Breakfasts: 2
- Target Health Score: 7.0
- Max High Effort Meals: 1

**This Week's Preferences (Updated Weekly):**
- Pantry Surplus: "Have extra ground beef"
- Recipe Search Preferences: "Want tacos this week"

**Shopping List Additions:**
- Paper towels (Include this week ✓)
- Dish soap (Include this week ✓)
- Milk (Always include ✓)
- Eggs (Always include ✓)

### Step 2: Automation Runs (Sunday 9am MST)

**Make.com Scenario Execution:**

1. **Retrieve Data** (Modules 2-4)
   - Fetched user preferences
   - Retrieved 70 recipes from database
   - Got shopping list additions

2. **Aggregate Recipe Data** (Modules 7-8)
   - Combined all recipe metadata into text format
   - Prepared for Claude API call

3. **Claude API Call #1 - Recipe Selection** (Module 5)
   - **Input:** User preferences + recipe database + current date
   - **Processing time:** ~3 seconds
   - **Output:** JSON with 9 recipe IDs + reasoning

4. **Parse JSON Response** (Module 16)
   - Extracted dinner_recipes array: 9 items (5 mains + 4 sides)
   - Extracted lunch_recipes array: 2 items
   - Extracted breakfast_recipes array: 2 items
   - Extracted health_score: 7.1
   - Extracted reasoning message

5. **Retrieve Ingredients** (Modules 17-25)
   - Iterator loops through each dinner recipe
   - Fetched ingredient lists from Airtable
   - Aggregated into single text block
   - (Repeated for lunches and breakfasts)

6. **Claude API Call #2 - Shopping List** (Module 26)
   - **Input:** All ingredients + shopping list additions
   - **Processing time:** ~2 seconds
   - **Output:** Formatted shopping list with categories

7. **Create Weekly Plan** (Module 27)
   - Created new record in Weekly Plans table
   - Linked 13 recipes (9 dinners, 2 lunches, 2 breakfasts)
   - Stored shopping list
   - Saved reasoning message

8. **Update Recipe Metadata** (Modules 28-37)
   - Dinners: Updated Last Planned Date for 9 recipes
   - Lunches: Updated Last Planned Date for 2 recipes  
   - Breakfasts: Updated Last Planned Date for 2 recipes

9. **Cleanup** (Modules 32-34)
   - Unchecked "Include this week?" for paper towels and dish soap
   - Left "Always Include" items checked (milk, eggs)

**Total Execution Time:** ~12 seconds

### Step 3: User Review (Manual)

**User opens Airtable:**
- Reviews weekly plan
- Checks shopping list
- Reads Claude's reasoning

**Optional adjustments:**
- Swap a recipe: Drag different recipe into plan
- Click "Regenerate Shopping List" button
- System recalculates shopping list with new recipes

**Typical outcome:** 
- 80% of weeks: No changes needed, use plan as-is
- 15% of weeks: Swap 1 recipe, regenerate list
- 5% of weeks: Significant changes (user had unexpected schedule change)

---

## Data Tracking Over Time

### Recipe Variety Tracking

**Last Planned Dates (Sample):**

| Recipe Name | Last Planned Date | Days Since |
|-------------|-------------------|------------|
| Korean Beef Bowl | 2026-01-13 | 0 days |
| Grilled Chicken Tacos | 2026-01-13 | 0 days |
| Baked Salmon | 2026-01-13 | 0 days |
| Spaghetti Bolognese | 2026-01-13 | 0 days |
| Pork Chops | 2026-01-13 | 0 days |
| Beef Stir Fry | 2026-01-06 | 7 days |
| Chicken Parmesan | 2026-01-06 | 7 days |
| Taco Salad | 2025-12-30 | 14 days |
| Meatloaf | 2025-12-23 | 21 days |
| Lasagna | 2025-12-16 | 28 days |

**Recency Rule in Action:**
- Recipes with Last Planned Date within 14 days are avoided
- Exception: If user explicitly requests (e.g., "want spaghetti again")
- Ensures natural rotation through recipe database

### Health Score Tracking

**Weekly Health Scores:**

| Week Starting | Target | Actual | Variance |
|---------------|--------|--------|----------|
| 2026-01-13 | 7.0 | 7.1 | +0.1 |
| 2026-01-06 | 7.0 | 6.9 | -0.1 |
| 2025-12-30 | 7.0 | 7.2 | +0.2 |
| 2025-12-23 | 7.0 | 6.8 | -0.2 |
| 2025-12-16 | 7.0 | 7.0 | 0.0 |

**Success Rate:** 95% of weeks within ±1 point of target

---

## Edge Cases Handled

### Edge Case 1: Not Enough Recent Recipes

**Scenario:** Only 8 lunch recipes in database, need 2 per week with 14-day recency

**Math:**
- 8 recipes / 2 per week = 4 weeks to rotate through all
- After 4 weeks, oldest recipe is 28 days old
- 28 days > 14 day recency window ✓

**Outcome:** System has sufficient rotation capacity

### Edge Case 2: User Requests Conflicting Constraints

**User Input:**
- Recipe Search Preferences: "Want quick meals, nothing over 30 minutes"
- Target Health Score: 9.0 (very high)

**Problem:** High health score recipes typically require more prep time (fresh vegetables, complex cooking methods)

**Claude's Response:**
```json
{
  "success": true,
  "health_score": 8.2,
  "reasoning": "Selected quickest high-health recipes available (salads, grilled proteins). Achieved 8.2 health score vs 9.0 target due to constraint conflict between speed and health. Prioritized user's explicit request for quick meals."
}
```

**Outcome:** System explains trade-off made, prioritizes explicit user request

### Edge Case 3: Pantry Surplus Dominates

**User Input:**
- Pantry Surplus: "Have 5 lbs ground beef to use up"

**Risk:** Could select ground beef recipes 5 nights in row (poor protein variety)

**Claude's Behavior:**
- Selected 3 ground beef recipes (Korean Beef, Taco Salad, Spaghetti)
- Balanced with 2 other proteins (chicken, fish)
- **Reasoning:** "Used ground beef surplus in 3/5 dinners while maintaining protein variety"

**Outcome:** Honors user request without violating variety constraints

### Edge Case 4: Manual Recipe Swap Breaks Side Pairings

**User Action:**
- Swaps "Pork Chops" with "Grilled Steak"
- Original plan had Pork Chops + Mashed Potatoes + Garden Salad
- Clicks "Regenerate Shopping List"

**System Behavior:**
- Keeps existing sides (Mashed Potatoes + Garden Salad) since user didn't change them
- Regenerates shopping list with steak ingredients instead of pork
- Removes pork chops from shopping list
- Adds steak to shopping list

**Outcome:** User has control, system adapts intelligently

---

## Success Metrics

**From 6+ Weeks of Production Use:**

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Meal count accuracy | 100% | 100% | ✓ |
| Meal type integrity | 100% | 100% | ✓ |
| Recency rule compliance | 90% | 97% | ✓ |
| Health score within target | 90% | 95% | ✓ |
| No manual intervention needed | 70% | 80% | ✓ |
| Overall success rate | 95% | 98% | ✓ |
| Time saved per week | 30 min | 40 min | ✓ |

**Failure Cases (2% failure rate):**

**Failure 1:** User requested "Italian food" and "Asian food" in same week with only 5 dinners
- **Issue:** Not enough variety in database to satisfy both preferences
- **Result:** Claude prioritized Italian (more specific request came first)
- **User action:** Manually added Thai recipe, regenerated list

**Failure 2:** Database only had 1 fish recipe available, user wanted 2 fish dinners
- **Issue:** Insufficient recipes in database
- **Result:** Claude returned success: false with explanation
- **User action:** Added more fish recipes to database for future weeks

---

## What This Demonstrates

**For Technical Audiences:**
- Multi-constraint optimization using LLMs
- Production-grade automation with error handling
- Iterative debugging of non-deterministic systems
- Workflow orchestration across multiple platforms

**For Non-Technical Audiences:**
- AI solving real daily problems
- Automation that saves measurable time
- System that learns and adapts over time
- Practical application of "AI agents"

**For Hiring Managers:**
- Hands-on AI implementation experience
- Systems thinking and architecture skills
- Ability to iterate from "working" to "production-ready"
- Documentation and explanation skills
- Builder mentality - solving problems by building solutions
