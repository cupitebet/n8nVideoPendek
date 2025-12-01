# 👤 Character Consistency Guide - Blueprint V2

**Masalah terbesar AI video: Karakter yang berubah-ubah di setiap frame**

**Solusi: Gemini 2.5 Flash Image + Veo 3.1 "Ingredients"**

---

## Kenapa Karakter Tidak Konsisten?

Saat Anda generate video AI clip per clip, setiap clip menghasilkan karakter baru:
- 😕 Wajah berbeda
- 😕 Warna rambut berubah
- 😕 Pakaian tidak sama
- 😕 Gaya visual tidak match

**Ini membuat video terlihat tidak profesional!**

---

## Solusi Blueprint V2

### 1. Create Character Sheet (Reference Image)

**Step 1: Generate Character Sheet**

Gunakan prompt khusus untuk Gemini 2.5 Flash Image:

```
Character Sheet of [Name], [description], multiple angles:
- Front view facing camera
- Side profile (left)
- Side profile (right)
- Three-quarter view
- Full body standing pose

Character details:
- [Age, gender, ethnicity]
- Distinct facial features: [round face, sharp jawline, etc]
- Hair: [color, style, length]
- Eyes: [color, shape]
- Clothing: [specific outfit with details]
- Accessories: [glasses, jewelry, etc]

Art style: [photorealistic / anime / 3D render / cartoon]
Lighting: Flat, even studio lighting
Background: Neutral gray background
Quality: High detail, 4K, professional character design reference
```

**Contoh Konkret:**

```
Character Sheet of Alex, young Asian gamer, multiple angles:
- Front view facing camera
- Side profile (left)
- Side profile (right)
- Three-quarter view
- Full body standing pose

Character details:
- Age 22, male, Asian (Chinese)
- Distinct facial features: round face, soft jawline, friendly smile
- Hair: short black hair with side part, slightly messy gamer style
- Eyes: dark brown, wide and expressive behind round wire-frame glasses
- Clothing: red gaming hoodie with white "AG" logo on chest, black jeans
- Accessories: round wire-frame glasses, gaming headset around neck

Art style: photorealistic digital illustration
Lighting: Flat, even studio lighting
Background: Neutral gray background
Quality: High detail, 4K, professional character design reference
```

**Output: 1 image dengan 4-5 views dari karakter yang sama**

---

### 2. Use Character Sheet sebagai Reference

**Step 2: Upload ke Google Cloud Storage**

```bash
# Upload character sheet ke bucket
gsutil cp character_alex.png gs://YOUR_BUCKET/characters/alex.png

# Get public URL
# gs://YOUR_BUCKET/characters/alex.png
```

**Step 3: Gunakan di Veo 3.1 Request**

Di n8n workflow, tambahkan reference image di setiap video generation:

```json
{
  "instances": [
    {
      "prompt": "{{ $json.visual_prompt }}",
      "image_storage_uri": "gs://YOUR_BUCKET/characters/alex.png",
      "audio_prompt": "{{ $json.sound_effect_prompt }}"
    }
  ],
  "parameters": {
    "sampleCount": 1,
    "videoLength": "5s",
    "aspectRatio": "9:16",
    "imageControlMode": "character_reference"
  }
}
```

**Key: `image_storage_uri` memastikan Veo 3.1 menggunakan karakter yang sama!**

---

### 3. Consistency Tips untuk Prompts

Saat menulis visual prompts, **selalu mention karakteristik yang sama**:

**❌ BURUK (tidak konsisten):**
```
"A guy playing games"
"Someone typing on keyboard"
"A person celebrating victory"
```

**✅ BAGUS (konsisten):**
```
"Alex, young Asian male with round glasses and red gaming hoodie,
playing intense game with focused expression, cinematic lighting"

"Same character Alex in red hoodie and glasses, frantically typing
on mechanical keyboard, blue screen glow, over-shoulder shot"

"Alex celebrating victory, jumping with arms raised, wearing same
red hoodie and glasses, confetti falling, wide angle shot"
```

**Selalu mention:**
1. ✅ Nama karakter
2. ✅ Key features (glasses, hoodie, dll)
3. ✅ "same character" untuk reinforcement

---

## Advanced: Multiple Characters

Jika video punya lebih dari 1 karakter:

### Strategy A: Multiple Character Sheets

```
# Create separate sheets
- character_alex.png (protagonist)
- character_rival.png (antagonist)
- character_mentor.png (supporting)

# Upload semua ke Cloud Storage
```

### Strategy B: Group Character Sheet

```
Character Group Sheet featuring:

1. Alex (protagonist):
   - Young Asian male, 22 years old
   - Round glasses, red gaming hoodie
   - Short black hair, friendly expression

2. Zara (rival):
   - Young Latina female, 24 years old
   - Purple streaked hair in ponytail
   - Black leather jacket, competitive smirk

3. Dr. Chen (mentor):
   - Older Asian male, 50s
   - Gray hair, lab coat over hoodie
   - Wise expression, slight beard

Standing together in lineup, same art style, neutral background
```

**Dalam prompt, specify which character:**
```
"Alex (young Asian male with round glasses and red hoodie) competing
against Zara (Latina with purple hair and black jacket), intense
gaming tournament, dramatic lighting"
```

---

## Testing Character Consistency

### Test Protocol

Generate 5 clips dengan karakter yang sama:

1. **Close-up** - Face detail test
2. **Medium shot** - Body + face test
3. **Action shot** - Movement test
4. **Different lighting** - Lighting consistency test
5. **Different angle** - Angle consistency test

### Evaluation Checklist

✅ Wajah recognizable across all clips?
✅ Clothing consistent?
✅ Hair style dan color sama?
✅ Accessories (glasses, dll) present?
✅ Overall vibe/personality consistent?

**If NO to any: Revise character sheet atau prompts!**

---

## Common Issues & Solutions

### Issue 1: Wajah Masih Berubah

**Solution:**
- Make character sheet MORE detailed
- Add MORE distinctive features (scar, tattoo, unique glasses)
- Increase "distinctive facial features" description
- Use photorealistic style (lebih konsisten daripada cartoon)

### Issue 2: Clothing Berubah

**Solution:**
- Specify EXACT clothing di setiap prompt
- Add brand details (logo, text on hoodie)
- Mention color explicitly (not just "hoodie", but "RED hoodie")

### Issue 3: Veo Ignoring Reference Image

**Solution:**
- Check `image_storage_uri` format benar (gs://...)
- Pastikan image accessible (public atau service account punya akses)
- Add `"imageControlMode": "character_reference"` di parameters
- File size < 10MB, format PNG/JPG

### Issue 4: Style Berubah Antar Clips

**Solution:**
- Lock art style di character sheet
- Add style keywords di SETIAP visual prompt:
  - "photorealistic style"
  - "same 3D render style"
  - "consistent anime art style"

---

## Pro Tips

### 1. Reuse Character Library

Buat library karakter untuk reuse:

```
/characters
  /alex_gamer
    - character_sheet.png
    - description.txt
    - sample_prompts.txt
  /zara_hacker
    - character_sheet.png
    - description.txt
    - sample_prompts.txt
```

### 2. Template Prompts

Gunakan template:

```
[CHARACTER_NAME], [CHARACTER_KEY_FEATURES], [ACTION],
[CAMERA_ANGLE], [LIGHTING], [ART_STYLE]

# Example:
Alex, young Asian male with round glasses and red gaming hoodie,
frantically typing on keyboard with intense focus,
over-shoulder close-up shot,
blue screen glow with dramatic rim lighting,
photorealistic digital art style
```

### 3. Version Control Character Sheets

```bash
# Naming convention
character_alex_v1.png  # Initial design
character_alex_v2.png  # Refined after testing
character_alex_final.png  # Production version
```

### 4. A/B Test Styles

Test different art styles untuk consistency:

| Style | Consistency | Cost | Speed |
|-------|-------------|------|-------|
| Photorealistic | ⭐⭐⭐⭐⭐ | $$$ | Slow |
| 3D Render | ⭐⭐⭐⭐ | $$ | Medium |
| Anime | ⭐⭐⭐ | $ | Fast |
| Cartoon | ⭐⭐ | $ | Fast |

**Recommendation: Photorealistic atau 3D Render untuk best consistency**

---

## Example Workflow

### Full Process untuk 1 Video

**Input:** Topic "AI Gaming Revolution"

**Step 1: Define Characters**
```
Main: Alex (gamer)
Supporting: AI Assistant (hologram)
```

**Step 2: Generate Character Sheets**
```
Prompt Gemini 2.5 Flash:
- Character sheet for Alex
- Character sheet for AI Assistant
```

**Step 3: Upload to Cloud Storage**
```bash
gsutil cp alex.png gs://bucket/alex.png
gsutil cp ai_assistant.png gs://bucket/ai_assistant.png
```

**Step 4: Generate Script (The Director)**
```
5 scenes featuring Alex and AI Assistant
```

**Step 5: Generate Videos (Veo 3.1)**
```json
Scene 1: reference alex.png
Scene 2: reference alex.png
Scene 3: reference alex.png + ai_assistant.png
Scene 4: reference alex.png
Scene 5: reference alex.png + ai_assistant.png
```

**Result: Consistent characters across all 5 clips! 🎉**

---

## Resources

- 📖 [Gemini Image Generation Docs](https://ai.google.dev/gemini-api/docs/vision)
- 📖 [Veo 3.1 Documentation](https://cloud.google.com/vertex-ai/docs/generative-ai/video/overview)
- 🎨 [Character Design Best Practices](https://www.youtube.com/results?search_query=character+design+consistency)

---

## Next Steps

- ✅ Create your first character sheet
- ✅ Test 5 clips with same character
- ✅ Evaluate consistency
- ✅ Iterate until perfect!

**Karakter konsisten = Video profesional! 🎬**
