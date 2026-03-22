# Calorie Helper - User Requirements Document

## Project Overview

**Project Name:** Calorie Helper (卡路里幫手)  
**Version:** 1.0 (Chinese Stable)  
**Platform:** Single Page Web Application  
**Purpose:** Help Hong Kong people lose weight by calculating calories, planning meals, and providing supermarket pricing information.

---

## 1. Page Structure

The application has 6 main pages accessible via tabs:

1. **Home (主頁)** - Landing page
2. **Profile (個人資料)** - Personal data input and BMR/TDEE calculation
3. **Food Search (食物搜尋)** - Search and browse food database
4. **Calculator (卡路里計算機)** - Manual calorie calculation with cooking methods
5. **Meal Plan Generator (自動生成餐單)** - AI-powered meal plan generation
6. **Cha Chaan Teng (茶餐廳)** - Hong Kong restaurant food calories

---

## 2. Page Details

### 2.1 Home Page (主頁)

**Purpose:** Landing page explaining the app

**Components:**
- Hero section with app title "卡路里幫手"
- Tagline: "為香港人而設的個人化減肥方案" (Personalized weight loss solution for Hong Kong people)
- "開始使用" (Get Started) button → navigates to Profile page
- Features section explaining 3 main functions:
  - BMR Calculator (基礎代謝率計算)
  - Meal Planner (餐單制定)
  - Supermarket Prices (超市價錢)
- How to use section with 3 steps:
  1. Enter personal data
  2. Search local foods
  3. Get meal plan
- Footer with GitHub link and data sources

---

### 2.2 Profile Page (個人資料)

**Purpose:** Collect user data to calculate daily calorie needs

**Input Fields:**
| Field | Type | Default | Validation |
|-------|------|---------|-------------|
| Age (年齡) | Number | 25 | 15-100 |
| Height (身高) | Number | 170 cm | 100-250 cm |
| Weight (體重) | Number | 70 kg | 30-300 kg |
| Gender (性別) | Select | Male | Male/Female |
| Activity Level (活動量) | Select | Moderate | 4 options |

**Activity Level Options:**
| Value | Label | Description |
|-------|-------|-------------|
| 1.2 | 很少運動 | Sedentary (little or no exercise) |
| 1.375 | 輕微運動 | Light (exercise 1-3 days/week) |
| 1.55 | 適中運動 | Moderate (exercise 3-5 days/week) |
| 1.725 | 積極運動 | Active (exercise 6-7 days/week) |

**Button Action:**
- "計算 BMR 同 TDEE" (Calculate BMR & TDEE)

**Formula Logic (BMR Calculation):**

```
For Male:
  BMR = (10 × weight_kg) + (6.25 × height_cm) - (5 × age) + 5

For Female:
  BMR = (10 × weight_kg) + (6.25 × height_cm) - (5 × age) - 161
```

**Formula Logic (TDEE Calculation):**
```
TDEE = BMR × Activity_Level
```

**BMI Calculation:**
```
BMI = weight_kg / (height_m)²
```

**Output Display:**
After calculation, shows:
- BMI value with category:
  - < 18.5: 體重過輕 (Underweight)
  - 18.5-24.9: 正常 (Normal)
  - 25-29.9: 輕度過重 (Overweight)
  - ≥ 30: 過重 (Obese)
- BMR value
- TDEE value
- Target calories (based on selected goal)

**Goal Selection:**
- 減肥 (Lose weight): TDEE - 500 calories
- 維持 (Maintain): TDEE calories
- 增肌 (Gain muscle): TDEE + 300 calories

**Auto-Navigate:**
After calculation, automatically redirects to Meal Plan Generator page after 500ms

**Data Persistence:**
- Saves to localStorage as JSON: `{"bmr": number, "tdee": number, "target": number, "bmi": number}`
- Key: `userStats`

---

### 2.3 Food Search Page (食物搜尋)

**Purpose:** Browse and search food database with calories and prices

**Components:**
- Search input box (filters by food name)
- Category filter buttons:
  - 全部 (All)
  - 肉類 (Meat)
  - 魚類 (Fish)
  - 海鮮 (Seafood)
  - 蔬菜 (Vegetables)
  - 水果 (Fruits)
  - 主食 (Staples)
  - 蛋類 (Eggs)
  - 豆類 (Beans)
  - 奶類 (Dairy)
  - 芝士 (Cheese)

**Display Format (per food item):**
```
[Food Name]
[Weight] | [Category]

HK$[Price]    [Calories] 卡
```

**Food Database:** 40+ items with:
- Name (Chinese)
- Price (HK$)
- Weight/Unit
- Source (惠康/百佳 - Wellcome/ParknShop)
- Category
- Calories per 100g

---

### 2.4 Calculator Page (卡路里計算機)

**Purpose:** Manually calculate calories for specific foods

**Input Components:**

1. **Food Selection Dropdown**
   - List of all foods from database
   - Default: empty (user must select)

2. **Weight Input**
   - Type: Number
   - Default: 100g
   - Range: 10-1000g

3. **Cooking Method Selector**
   | Method | Calorie Addition |
   |--------|-----------------|
   | 白灼/水煮 (Boiled) | +0 |
   | 蒸 (Steamed) | +0 |
   | 烤/焗 (Baked) | +0 |
   | 煎 (Pan-fried) | +40 |
   | 炒 (Stir-fried) | +60 |
   | 炸 (Deep-fried) | +150 |

4. **Sauce/Seasoning Multi-Select**
   | Sauce | Calories |
   |-------|----------|
   | 生抽 (Light soy) | +3 |
   | 老抽 (Dark soy) | +5 |
   | 蠔油 (Oyster sauce) | +10 |
   | 麻油 (Sesame oil) | +15 |
   | 食用油 (Cooking oil) | +40 |
   | 辣椒醬 (Chili sauce) | +5 |
   | 沙律醬 (Salad dressing) | +35 |
   | 茄汁 (Ketchup) | +8 |
   | 咖喱醬 (Curry sauce) | +15 |
   | 蜜糖 (Honey) | +21 |

**Button Actions:**

1. **加入餐單 (Add to Meal)**
   - Validates food is selected
   - Calculates: `(base_calories × weight / 100) + cooking_calories + sauce_calories`
   - Adds item to meal list
   - Updates total calories and price

2. **Remove Item (✕)**
   - Removes specific item from meal list
   - Recalculates totals

**Display:**
- List of added items showing: food name, weight, cooking method, calories
- Running total: Total calories + Total price (HK$)

---

### 2.5 Meal Plan Generator Page (自動生成餐單)

**Purpose:** Generate personalized daily meal plan

**Input Components:**

1. **Goal Selector**
   - 減肥 (Lose weight): -500 cal/day
   - 維持 (Maintain): TDEE
   - 增肌 (Gain muscle): +300 cal/day

2. **Diet Preference Dropdown**
   - 均衡飲食 (Balanced)
   - 低碳水化合物 (Low carb)
   - 高蛋白質 (High protein)
   - 素食 (Vegetarian)

**Button Action:**
- "生成餐單 ✨" (Generate Meal Plan)

**Generation Logic:**

1. Get target calories from userStats (must have calculated Profile first)
2. Distribute calories:
   - Breakfast: 25%
   - Lunch: 35%
   - Dinner: 30%
   - Snack: 10%

3. For each meal, randomly select:
   - Protein source: from meat/fish/beans categories
   - Carb source: from staple category
   - Vegetable: from vegetable category

4. Calculate portions:
   - Protein portion: `(meal_calories × 0.4) / protein_calories_per_100g × 100`
   - Carb portion: `(meal_calories × 0.35) / carb_calories_per_100g × 100`
   - Vegetable: 1 serving

5. Estimate price per meal

**Output Display:**
```
[Meal Name] (X calories)
  [Protein Food] - Xg
  [Carb Food] - Xg
  [Vegetable] - 1份

... (repeats for each meal)

Daily Total: XXXX calories | HK$XX
```

**Validation:**
- If userStats not calculated: show alert and redirect to Profile

---

### 2.6 Cha Chaan Teng Page (茶餐廳)

**Purpose:** Display common Hong Kong restaurant food calories

**Display Format:**
```
[Food Name]    [Calories] 卡
```

**Food List:**
- 叉燒飯 (Char siu rice): 650 cal
- 咖喱牛腩飯 (Curry beef rice): 720 cal
- 乾炒牛河 (Stir-fried rice noodles): 580 cal
- 蛋撈飯 (Egg fried rice): 450 cal
- 奶茶 (Milk tea): 120 cal
- 鴛鴦 (Yuenyeung coffee+tea): 180 cal
- 餐蛋公仔麵 (Instant noodles with egg): 520 cal
- 西多士 (French toast): 480 cal

---

## 3. Global Features

### 3.1 Language Toggle

**Buttons:**
- 中文 (Chinese)
- EN (English)

**Functionality:**
- Switches between Chinese and English UI text
- Persists to localStorage key: `lang`
- Updates: tab labels, button text, placeholder text

### 3.2 Dark Mode Toggle

**Button:** 🌙 (moon) / ☀️ (sun)

**Functionality:**
- Toggles dark mode class on `<html>` element
- Persists to localStorage key: `darkMode`
- Affects: background colors, text colors, card backgrounds

### 3.3 Tab Navigation

**Tabs:**
- 主頁 (Home)
- 個人資料 (Profile)
- 食物搜尋 (Food Search)
- 卡路里計算機 (Calculator)
- 自動生成餐單 (Meal Plan)
- 茶餐廳 (Cha Chaan Teng)

**Behavior:**
- Click tab → shows corresponding page
- Active tab highlighted with primary color
- Previous page automatically hidden

---

## 4. Data Storage

**localStorage Keys:**

| Key | Format | Description |
|-----|---------|-------------|
| `userStats` | `{"bmr": number, "tdee": number, "target": number, "bmi": number}` | Calculated user metrics |
| `lang` | `"zh"` or `"en"` | Current language |
| `darkMode` | `"true"` or `"false"` | Dark mode state |

---

## 5. Technical Specifications

**Framework:** Vanilla HTML + JavaScript (no frameworks)  
**Styling:** Tailwind CSS (via CDN)  
**Hosting:** GitHub Pages  
**Repository:** roxoctoclaw/calorie-web

---

## 6. Acceptance Criteria

1. ✅ All 6 pages accessible via tabs
2. ✅ Profile calculation produces correct BMR, TDEE, BMI values
3. ✅ Food search filters by category and search text
4. ✅ Calculator correctly adds cooking and sauce calories
5. ✅ Meal plan generator creates balanced meals
6. ✅ Language toggle switches all UI text
7. ✅ Dark mode toggles all page colors
8. ✅ Data persists across page refreshes (localStorage)
9. ✅ Responsive design works on mobile and desktop
10. ✅ All food items display name, price, calories, category
