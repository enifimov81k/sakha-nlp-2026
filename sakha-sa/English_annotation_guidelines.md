# Yakut Sentiment Analysis Annotation Guidelines

## 1. Task

Annotate the sentiment of each utterance as one of three categories:

- **Positive**
- **Negative**
- **Neutral**

Label according to the author’s explicit or implicit evaluation.  
The target or topic of evaluation does **not** need to be specified.

---

## 2. Unit of Annotation

- Each **line** in the file corresponds to **one example**.  
- If the line is only a **fragment** but expresses a sentiment, annotate it.  
- If the text has **no meaning** or is **unreadable**, set `sentiment = Skip`  
  and provide a short reason in the `comment` column.

---

## 3. Class Definitions

### Positive

Expressions of praise, approval, joy, gratitude, relief, or hopeful tone.

**Examples**
- sah: `Уай наһаа үчүгэй`, `Уруй айхал!`, `Эрдэттэн махтал`,  
  `Айылҕа тыыннааҕа хайа баҕарар омукка – дьоло`
- Emojis or markers: `)))`, `😊`, `ахаха` (without irony)

---

### Negative

Expressions of criticism, complaint, irritation, sadness, fear, anger,  
or any negative evaluation.

**Examples**
- sah: `Пахай куһаҕан баҕайы`, `Иккис эһэм кулаактанан сыылкаҕа бараахтаабыт`,  
  `Эдэр дьон үөрэхтэрин бүтэрбиттэрин да иһин, тыа сиригэр төннүбэттэр`,  
  `Оттон үөр ыт сүрдээх кутталлаах`
- Markers: `((`, `😡`, `жесть` (in the context of complaint or anger)

---

### Neutral

Facts without evaluation, unemotional questions, announcements,  
contact details, prices, dates, or quotations without stance.

**Examples**
- sah: `Биткоин ахсаана эмиэ хааччахтаах – 21 мөл.`
- sah: `Төһө элбэх сайабылыанньа киирдэ?`

**Default rule:**  
If there is **no explicit or implicit evaluation**, assign **Neutral**.

---

## 4. Common Cases and How to Resolve Them

### 4.1 Questions
- **Neutral factual question →** Neutral  
  *Example:* `Лешаны ким билэр?`
- **Complaint disguised as question →** Negative  
  *Example:* `Арай тэрилтэ сабыллан хааллын, төһөлөөх киһи үлэтэ суох хаалыай?`
- **Rhetorical or sarcastic question** without clear markers or context → Neutral

---

### 4.2 Requests and Pleas
- **Request for help due to problem →** Negative  
  *Example:* `Көмөлөһүн кыайан киирбэппин`
- **Neutral request for information →** Neutral  
  *Example:* `Кинигэлэри ханна атылыллар?`

---

### 4.3 Irony and Sarcasm
- If irony or sarcasm is clearly marked (mocking tone, sarcastic emojis, etc.),  
  classify by **actual sentiment** — usually **Negative**.  
- If uncertain, assign the most likely class in `sentiment`  
  and add `uncertain` in `comment`.

---

### 4.4 Emojis and Repeated Punctuation
- `!!!`, `???`, ALL CAPS, or elongated words (`круууто`) **intensify**  
  an existing sentiment but **do not change** its class.  
- `)))` without context → usually **Positive**  
- `((` → usually **Negative**

---

### 4.5 Indirect Evaluation
- `Бүтэһигэр көннөрдүлэр` → Positive  
- `Эмиэ алдьаммыта` → Negative

---

### 4.6 Mixed Sentiment
If both positive and negative cues appear:
- Choose the **dominant** sentiment.  
- If perfectly balanced (≈50/50), label **Neutral** and add `uncertain`.

---

### 4.7 Announcements, Contacts, Prices
- No evaluation → **Neutral**  
- Meaningless text, contact info, or links only → **Skip**

---

### 4.8 Profanity
Usually **Negative**, unless it’s clear friendly banter with a positive tone.

---

## 5. Decision Process

1. **Understandable text?**  
   - No → `Skip`
2. **Contains evaluation or emotion?**  
   - No → `Neutral`
3. **If yes, decide:** Positive or Negative  
4. **If uncertain,** assign the most probable class  
   and write `uncertain` with a short comment.

---

## 6. Technical Rules

- Do **not** correct spelling or grammar.  
- Do **not** add any external context.  
- If the text contains only personal data (no evaluation),  
  mark **Neutral** or **Skip** depending on meaning.  
- Keep comments **short and relevant**.

---

## 7. Quick Reference

| Situation | Label |
|------------|--------|
| No evaluation | Neutral |
| Praise, gratitude, joy | Positive |
| Complaint, anger, fear, irritation | Negative |
| Nonsense or corrupted text | Skip |
| Language specified in `lang` | — |
| Uncertain case | `uncertain` + short comment |
