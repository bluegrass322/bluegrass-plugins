---
name: proofreading
description: Proofreads documents for typos and omissions, inconsistent writing style, notational inconsistency, misused expressions, grammatical errors and formal inconsistencies, then produces a report that classifies each finding as False or Vague and suggests corrections. Use when the user asks to proofread a document, check for typos or 表記揺れ, verify whether expressions are used correctly, or confirm that a text is correct.
arguments: [target]
context: fork
model: opus
allowed-tools: Artifact, Glob, Grep, Read, TaskCreate, TaskGet, TaskList, TaskUpdate, WebFetch, WebSearch
---

# Proofreading

## Instructions

Find and report errors such as typos and misused expressions in the document $target, to improve its readability and reliability. The user will make the corrections themselves, so do not edit the document. Your task ends once you have reported the items that need fixing.

Detect errors only. Do not judge the content, for example whether it is good or bad.

The aim of proofreading is text that reads naturally to its readers. The opposite of misuse is not "correct" usage but established usage. So do not treat a usage as an error if it is widely established, even if dictionaries consider it incorrect.

Carry out Steps 1–6 one at a time, in order, and track progress with the Task tools. Do not work on several steps at once. In each step, review the entire document from that step's perspective only. Footnotes and captions are in scope. Classify each finding using these criteria:

| Result | Criterion |
| --- | --- |
| True | Not an error. Do not include it in the report |
| False | Very likely an error. Include a correction in the report |
| Vague | Possibly an error, but this cannot be confirmed. Give the reason in the report and leave the decision to the user |

### Step 1: Consistent writing style (Category: 文体の不統一)

Check whether the whole document consistently uses one style: either the polite style (ですます調) or the plain style (である調). Treat the majority style as the standard and report the sentences written in the minority style. Exclude quotations, bullet points, headings and sentences ending in a noun (体言止め).

### Step 2: Typos and omissions (Category: 誤字脱字)

Break every sentence down to word level and check it for typos and omissions.

Examples: conversion errors such as "以上" becoming "異常" or "始め" becoming "初め", missing characters, and duplicated words.

Take particular care with proper nouns, and check them as follows:

- Check that they match the official form exactly, including letter case, symbols, and the choice of hiragana or katakana (e.g. "iphone" → "iPhone", "にっぽん放送" → "ニッポン放送").
- Check that trademarks are not used as generic terms (e.g. "マジック" used to mean any permanent marker).
- Remember that an official form may differ from the usual spelling (e.g. "キユーピー", "富士フイルム").

If you are not confident of a proper noun's official form, classify it as Vague.

### Step 3: Notational inconsistency (Category: 表記揺れ)

Group together words and phrases that refer to the same thing or concept. Within each group, find forms that are not consistent and classify each one into one of these three types. Base the correction on the form most common in the document.

- Orthographic variation: same reading, different written form (e.g. "子ども" and "子供", "サーバー" and "サーバ")
- Lexical variation: different words referring to the same thing (e.g. "彼岸花" and "曼珠沙華", "スマートフォン" and "スマホ")
- Phrasal variation: different word combinations or constructions at phrase level (e.g. "電源を入れる" and "電源をオンにする")

If the type is unclear, apply these rules in order: if the reading is the same, it is orthographic; if replacing a single word makes them match, it is lexical; otherwise, it is phrasal.

If the variation may be intentional (for example, the full name only at first mention, or inside a quotation or proper noun), classify it as Vague.

### Step 4: Misused expressions (Category: 表現の誤り)

Check whether proverbs, idioms and compound words are used with a meaning different from their original one.

- A proverb's meaning is misunderstood (e.g. "情けは人の為ならず" used to mean "showing kindness to someone does them no good")
- A word is confused with a similar one (e.g. "役不足", which means "a role too minor for one's ability", used to mean "力不足")

If a usage differs from the original meaning but is widely established, classify it as Vague (e.g. "姑息" used to mean "卑怯な" rather than its original meaning, "一時しのぎ").

### Step 5: Grammatical errors (Category: 文法の誤り)

Find incorrect use of particles and mismatches between subject and predicate.

- 私はこの世界**に**好きだ → 私はこの世界**が**好きだ
- 金曜の夜**が**楽しんでいる → 金曜の夜**を**楽しんでいる

Do not focus so much on fixing the error that you change what the author meant to say.

### Step 6: Formal inconsistencies (Category: 形式的な矛盾)

Check for mismatches between the table of contents and the headings in the body, numbering errors in headings, figures, tables and notes, and in-text references (e.g. "表3参照") that do not match their target.

### Step 7: Consolidate the findings

Findings from Steps 1–6 may overlap or contain one another, so that fixing one makes another fix unnecessary.

Example: "私は子ども子供が好きだ。" contains a 表記揺れ, but removing the duplicated word also resolves it.

When fixing a parent item also resolves a child item, report only the parent item. If there are conflicting corrections for the same location, classify it as Vague and give both corrections in the Note.

### Step 8: Write the report

Before writing the report, confirm the following. If any point is not met, return to the relevant step.

- [ ] Steps 1–6 have been applied to the whole document, including footnotes and similar elements.
- [ ] Each Line No. and Item matches the corresponding line in the target document.
- [ ] Every False item has a Correction.
- [ ] Every Vague item has a Note explaining why it could not be decided.

Use the structure below. Write the report text in Japanese. The table headers may stay in English. List False rows first, then Vague rows, and sort rows with the same Result by line number. In Item, give the shortest phrase that contains the error.

Output example:
```markdown
## サマリー

- 対象文書: [対象文書のパス]
- 調査日: [YYYY-MM-DD]
- 判定件数: False [n] 件 / Vague [n] 件

[修正が必要な主な箇所を数行で要約する]

## 調査結果

| Item | Result | Line No. | Category | Correction | Note |
| ---- | ------ | -------- | -------- | ---------- | ---- |
| 異常の通り | False | 12 | 誤字脱字 | 以上の通り | |
| 子供 | False | 30 | 表記揺れ | 子ども | 文書内では "子ども" が8箇所で多数派 |
| 姑息な手段 | Vague | 45 | 表現の誤り | 卑怯な手段 | 本来の意味は "一時しのぎ" だが、"卑怯な" の意味でも広く定着している |
```

## References

If you are unsure of a word's meaning or a proper noun's official form, check these dictionaries:

- Japanese dictionary: https://kotobank.jp/
- English dictionary: https://dictionary.cambridge.org/
