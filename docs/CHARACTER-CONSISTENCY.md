# 👤 Character Consistency Guide - Blueprint V2

**Master the #1 challenge in AI video: Keeping characters consistent across scenes**

---

## The Problem

When generating multiple video clips for one story, AI models often create different-looking characters in each scene:

**Scene 1:** Alex has short black hair and round glasses
**Scene 2:** Alex suddenly has long brown hair and no glasses ❌
**Scene 3:** Alex is now blonde with different facial features ❌

This breaks immersion and looks unprofessional!

---

## The Solution

Use **Character Reference Images** + **Detailed Prompts** to maintain consistency.

### Two-Step Process:

1. **Create a Character Sheet** (one-time setup)
2. **Reference it in every scene** (automatic in workflow)

---

## Step 1: Create Character Sheet

### Using the Character Generator Workflow

**File:** `workflows/character-generator-cometapi.json`

**Steps:**
1. Open the workflow in n8n
2. Set character details:
   ```json
   {
     "character_name": "Alex Chen",
     "character_description": "young Asian male, 22 years old, short black hair, round glasses, red gaming hoodie, friendly expression"
   }
   ```
3. Execute workflow
4. Get character sheet image URL from output
5. Save this URL for use in main workflow

### Character Description Template

Use this structure for best results:

```
[Name], [Age/Gender], [Distinctive Features], [Clothing], [Expression/Mood]
```

**Examples:**

**Good ✅:**
```
Maya Rodriguez, young Hispanic female, 25 years old, 
long curly black hair, amber eyes, white lab coat over blue shirt, 
confident professional expression
```

**Bad ❌:**
```
A girl
```

**Why?** The detailed version gives AI consistent attributes to reference.

---

## Step 2: Reference in Prompts

### In Main Workflow

The main workflow (`youtube-shorts-cometapi.json`) automatically:

1. Generates character image for each scene
2. Uses that image as reference for Veo 3 video generation

**Your job:** Make sure visual prompts mention the SAME character details every time.

### Visual Prompt Formula

For each scene, use this structure:

```
[Character name with description], [Action], [Camera angle], 
[Lighting], [Mood], [Art style]
```

**Example:**

```
Alex Chen, young Asian male with round glasses and red gaming hoodie, 
frantically typing on mechanical keyboard, 
over-shoulder shot, 
blue screen glow lighting, 
intense focused mood,
photorealistic style
```

**Key:** Keep character description identical across all scenes!

---

## Best Practices

### 1. Define Character Once, Reuse Everywhere

Create a "character bible" document:

```markdown
# Character: Alex Chen

**Physical:**
- Young Asian male, 22 years old
- Short black hair, always neat
- Round glasses with black frames
- Height: average build

**Clothing:**
- Red gaming hoodie (primary outfit)
- Black jeans
- White sneakers

**Personality traits (for expressions):**
- Friendly but focused
- Confident but not arrogant
- Shows emotion through eyes (glasses!)
```

Use this reference when writing prompts.

### 2. Use Consistent Keywords

**Bad approach (inconsistent):**
- Scene 1: "young guy with glasses"
- Scene 2: "Asian programmer"
- Scene 3: "tech professional"

**Good approach (consistent):**
- Scene 1: "Alex Chen, young Asian male with round glasses and red hoodie"
- Scene 2: "Alex Chen, young Asian male with round glasses and red hoodie"
- Scene 3: "Alex Chen, young Asian male with round glasses and red hoodie"

Copy-paste the exact same description!

### 3. Focus on Distinctive Features

What makes your character unique?

**Examples of good distinctive features:**
- Round glasses
- Specific hair color/style
- Unique clothing item (red hoodie)
- Facial features (freckles, scar, dimples)
- Accessories (watch, necklace, hat)

Mention these in EVERY prompt.

### 4. Use Character Generator First

**Workflow:**

1. **Day 1:** Create 3-5 character sheets
   - Protagonist
   - Antagonist (if needed)
   - Supporting characters

2. **Day 2+:** Use these character URLs in all video generation
   - No need to regenerate characters
   - Reuse across multiple videos!

**Cost savings:** Character image costs ~$0.002. Generate once, use 100x!

---

## Advanced Techniques

### Multi-Character Scenes

When you have 2+ characters in one scene:

```
Scene with Alex Chen (young Asian male, round glasses, red hoodie) 
and Maya Rodriguez (young Hispanic female, long curly hair, white lab coat), 
both standing in futuristic lab, 
wide shot showing both characters, 
bright clinical lighting, 
collaborative teamwork mood,
photorealistic style
```

**Tip:** Still describe BOTH characters fully in every scene they appear.

### Character Transformations

If your story requires character change (costume, emotion):

**Define the change explicitly:**

```
Alex Chen (young Asian male, round glasses) wearing FORMAL black suit 
instead of usual red hoodie, standing confidently, 
full body shot,
warm office lighting,
professional serious expression,
photorealistic style
```

Note: We kept distinctive features (glasses) but changed clothing explicitly.

### Emotional Consistency

Characters should have consistent emotional expressions unless story requires change:

**Baseline expression:**
```
Alex Chen ... friendly focused expression
```

**Stressed scene:**
```
Alex Chen ... worried anxious expression, sweat on forehead
```

**Victory scene:**
```
Alex Chen ... bright victorious smile, fist pump gesture
```

---

## Testing Character Consistency

### The 3-Scene Test

Before creating a full video:

1. Generate 3 test scenes with the same character
2. Download all 3 videos
3. Check if character looks the same

**What to check:**
- ✅ Hair color and style matches
- ✅ Facial features similar
- ✅ Clothing matches description
- ✅ Accessories present (glasses, etc.)

If inconsistent, refine your prompts and try again.

---

## Common Issues & Solutions

### Issue #1: Character Changes Age Between Scenes

**Problem:** "young adult" becomes "teenager" or "middle-aged"

**Solution:** Be specific about age
- Instead of: "young person"
- Use: "25-year-old adult" or "early twenties"

### Issue #2: Clothing Changes Color

**Problem:** "Red hoodie" becomes pink or orange

**Solution:** Use specific color descriptors
- Instead of: "red hoodie"
- Use: "bright crimson red gaming hoodie"

### Issue #3: Facial Features Drift

**Problem:** Face looks different each time

**Solution:** 
1. Use Character Generator to create reference image
2. Ensure reference image URL is used in video generation
3. Add facial descriptors: "round face", "sharp jawline", "high cheekbones"

### Issue #4: Accessories Disappear

**Problem:** Glasses present in Scene 1, gone in Scene 2

**Solution:** Mention accessories in EVERY prompt
- Always include: "wearing round black-framed glasses"

---

## Character Sheet Prompt Templates

### Template 1: Professional Character

```
Character Sheet of [Name],
[Age] year old [Ethnicity] [Gender],
[Hair description],
[Eye description],
wearing [Professional outfit],
[Distinctive features],
multiple angles: front view, side profile left, side profile right, three-quarter view,
flat studio lighting,
neutral gray background,
photorealistic digital illustration style,
high detail 4K quality
```

### Template 2: Casual Character

```
Character Sheet of [Name],
[Age descriptor] [Gender],
[Hair and facial features],
wearing [Casual outfit],
[Expression/personality],
full turnaround views: front, left side, right side, back,
natural even lighting,
simple background,
consistent character design reference
```

### Template 3: Animated Style Character

```
Character Sheet of [Name],
[Physical description],
anime/cartoon style,
[Clothing description],
characteristic [expression],
front view, side view, three-quarter view,
clean line art,
white background,
professional character design sheet
```

---

## Workflow Integration

### In Character Generator Workflow

The workflow automatically creates character sheets using this prompt structure:

```javascript
Character Sheet of ${name}, ${description}, multiple angles:
- Front view facing camera
- Side profile (left)
- Side profile (right)
- Three-quarter view
- Full body standing pose

Art style: photorealistic digital illustration
Lighting: Flat, even studio lighting
Background: Neutral gray background
Quality: High detail, 4K
```

You just need to provide:
- `character_name`
- `character_description`

### In Main Video Workflow

Character consistency maintained by:

1. **Director Node:** Generates consistent character descriptions in visual_prompts
2. **Image Gen Node:** Creates scene image using that description
3. **Veo 3 Node:** Uses scene image as reference for video

**Your responsibility:** Ensure Director prompt (system instruction) emphasizes character consistency.

---

## Optimization Tips

### Batch Character Creation

Create all characters needed for your content niche upfront:

**Example - Tech Channel:**
1. Programmer character (hoodie, glasses)
2. CEO character (suit, confident)
3. User character (casual, confused)
4. Hacker character (dark hoodie, mysterious)

Store these character URLs in a spreadsheet:

| Character | Description | Image URL |
|-----------|-------------|-----------|
| Alex (Programmer) | young Asian male... | https://... |
| Sarah (CEO) | middle-aged... | https://... |

Reuse across videos!

### Cost Analysis

**Without character sheets:**
- Generate character image every scene: 5 scenes × $0.002 = $0.010/video
- Inconsistent results, need retries: +$0.020
- Total: ~$0.030/video

**With character sheets:**
- Generate character once: $0.002 (one-time)
- Reuse 100 videos: $0.002 ÷ 100 = $0.00002/video
- Consistent results, fewer retries: -$0.020 saved
- Total: ~$0.00002/video

**Savings: 99.9%!** Plus better quality!

---

## Checklist: Character Consistency

Before generating your video, verify:

- [ ] Character sheet created with detailed description
- [ ] Character sheet image URL saved
- [ ] Character description includes:
  - [ ] Age and gender
  - [ ] Ethnicity (if relevant)
  - [ ] Hair color and style
  - [ ] Distinctive facial features
  - [ ] Clothing description
  - [ ] Accessories (glasses, jewelry, etc.)
- [ ] Same character description used in ALL scenes
- [ ] Visual prompts include character name
- [ ] Art style consistent across prompts
- [ ] Character reference image URL used in video generation

---

## Examples

### Example 1: Tech Entrepreneur

**Character Sheet:**
```
Name: Marcus Johnson
Description: 35-year-old African American male, short curly black hair with gray streaks, thick-rimmed black glasses, athletic build, wearing navy blue blazer over white t-shirt, confident charismatic expression
```

**Scene Prompts:**

**Scene 1:**
```
Marcus Johnson, 35-year-old African American male with short curly hair and thick-rimmed glasses wearing navy blazer, presenting to audience with hand gestures, medium shot, stage lighting with spotlight, confident energetic mood, photorealistic style
```

**Scene 2:**
```
Marcus Johnson, 35-year-old African American male with short curly hair and thick-rimmed glasses wearing navy blazer, typing on laptop in modern office, over-shoulder shot, natural window lighting, focused concentrated mood, photorealistic style
```

**Scene 3:**
```
Marcus Johnson, 35-year-old African American male with short curly hair and thick-rimmed glasses wearing navy blazer, celebrating with team fist bumps, wide shot showing full body, bright office lighting, joyful victorious mood, photorealistic style
```

**Result:** Same character, different actions and angles!

---

## Resources

### Templates
- [Character Sheet Template](../prompts/character-sheet-template.txt)
- [Visual Prompt Template](../prompts/visual-prompt-template.txt)

### Examples
- [Sample Character Sheet](../examples/sample-character-sheet.json)
- [Sample Script with Consistent Characters](../examples/sample-script-output.json)

### Workflows
- [Character Generator Workflow](../workflows/character-generator-cometapi.json)
- [Main Video Workflow](../workflows/youtube-shorts-cometapi.json)

---

## Next Steps

1. ✅ Create your first 3 character sheets
2. ✅ Test with a simple 3-scene video
3. ✅ Review consistency
4. ✅ Refine descriptions based on results
5. ✅ Build character library for your niche
6. ✅ Reuse across multiple videos!

---

**Master character consistency = Professional-looking AI videos! 🎬**

*Consistency is key to making your AI-generated content feel real and engaging.*
