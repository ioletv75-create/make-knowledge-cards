---
name: make-knowledge-cards
description: Convert a pasted article or a local Markdown/TXT file into 5-8 knowledge cards. Each card carries a title, the core knowledge point, a plain-language explanation, and either a concrete example or a self-test question. Use when the user asks to make knowledge cards, study cards, flashcards, or asks to distill an article into key points for review or memorization. Triggers on pasted article text or a local .md/.txt path. Extraction only - never invents facts not present in the source.
license: MIT
---

# Make Knowledge Cards

## Overview

Turn one source text into 5-8 knowledge cards. The value of this skill is
selection and honesty: extract only what actually matters, keep one knowledge
point per card, and never add information the source does not contain. A short
article producing 3 excellent cards is a success; an article padded to 8 cards
with restated or invented content is a failure.

## Scope

Supported input:

- Article text pasted directly into the conversation.
- A local Markdown (`.md`) or plain text (`.txt`) file path.

Not supported (say so plainly instead of attempting it):

- Fetching or scraping web pages.
- PDF, DOCX, image, or audio input.
- Anki `.apkg` export or any flashcard-app format.
- A graphical interface or interactive editor.

If the user asks for an unsupported format, tell them what you can do with the
same content (paste the text, or point to a local `.md`/`.txt` file).

## Card fields

Every card contains exactly these four fields, in this order:

| Field | Role | Constraint |
| --- | --- | --- |
| Title | Names the single knowledge point | A noun phrase, not a sentence. No "Introduction" or "Summary". |
| Core knowledge | One-sentence claim worth remembering | Must be traceable to the source. No hedging filler. |
| Explanation | Why or how it works, in plain language | 1-3 sentences. Unpack jargon the source uses. |
| Example or question | Grounds the point | Exactly one of the two. Both must be self-contained and source-grounded. See the choice rule below. |

### Choosing example vs. self-test question

- Use an **example** when the source already supplies a concrete case, number,
  scenario, or piece of code. Reuse the source's own material.
- Use a **self-test question** when the source is abstract - a principle,
  definition, or rule with no illustration.
- Never manufacture an example the source does not support. When the source
  gives a formula but no worked case, write a question, not a made-up case.
- Exactly one of the two fields per card, never both. If a card carries an
  example, no question; if it carries a question, no example. A question and an
  example that teach the same thing in two forms is one card doing double duty.
- The example must be self-contained. Never write "同上一张卡片" or refer to
  another card's example; a reader who sees one card alone must understand it.

## Workflow

1. **Read the whole source before deciding anything.** Do not start drafting
   cards from the opening paragraphs; the most important point is often not
   first, and later sections frequently override earlier ones.

2. **Identify candidate knowledge points.** For each paragraph or section, ask:
   "If the reader forgot everything else, would forgetting this cost them
   something?" Keep only points that pass. Prefer mechanism, cause-effect,
   definitions that unlock other ideas, and non-obvious rules. Drop background,
   pleasantries, author biography, marketing, and rhetorical repetition.

3. **Rank by importance.** Order candidates by how much downstream
   understanding depends on them. Keep the top ones.

4. **Enforce one point per card.** If a candidate contains two independent
   claims joined by "and", split it or drop the weaker half. Two cards each
   teaching one thing beat one card teaching two.

   The discriminator against step 5: split when the two claims are independently
   useful - the reader could learn one and still need the other. Merge when one
   is merely a more specific or more general statement of the same claim.

5. **Merge duplicates.** The same claim stated in the introduction, the body,
   and the conclusion is one card. Merge paraphrases, synonyms, and worked
   restatements of a single idea.

   Also merge near-duplicates that differ only in generality - for example a
   card naming a set of regions and a second card stating the rule for moving
   data between those same regions are one card, not two. Split only when each
   half stands alone as something the reader could fail to understand
   separately. When two cards survive only because you can argue a distinction,
   merge them.

6. **Set the count honestly.** Aim for 5-8 cards. If fewer than 5 distinct,
   important points survive steps 2-5, deliver what you have and state the
   source did not support more. Never split one idea into two cards, inflate
   minor details, or import outside knowledge to reach 8. If far more than 8
   points survive, keep what matters most and note what you set aside.

7. **Draft the cards**, respecting the field constraints above.

8. **Run the quality gate** on the finished set before responding.

## Output format

Respond with the cards only. Use this shape:

```markdown
## 1. <Title>

- **核心知识**：<one-sentence claim>
- **简明解释**：<1-3 sentences>
- **例子**：<source-grounded example>

## 2. <Title>

- **核心知识**：...
- **简明解释**：...
- **自测**：<question the reader can answer from the source>
```

Language: write in the language of the source text, unless the user requests
otherwise. Field labels stay as shown (核心知识 / 简明解释 / 例子 / 自测) for a
Chinese source; for an English source use **Core**, **Explanation**,
**Example**, **Self-test**.

Add a closing note only when it carries information - for example "源文本较短，
仅支持生成 3 张卡片" or a list of important points set aside for the 8-card
ceiling. Do not add a note that just restates the card count.
## Quality gate

Check every card against all eight items. Fix failures before responding.

1. **Traceable** - every statement appears in, or follows directly from, the
   source. No outside facts, dates, numbers, or names.
2. **Single point** - the card teaches one thing.
3. **Non-duplicative** - no other card makes the same claim, or a more general
   or more specific version of it.
4. **Important** - removing it would cost the reader real understanding.
5. **Self-contained** - readable without the other cards, including its example.
6. **Exactly one grounding field** - an example or a question, not both.
7. **Concrete** - the example is real (from the source) or the question is
   answerable from the source alone.
8. **Honest count** - the number of cards reflects the source, not a target.

If a card fails item 1, delete it rather than soften it.

## Worked example

Source (abridged): *"A cache reduces latency because repeated reads are served
from fast memory instead of the origin. The cache must be invalidated when the
underlying data changes; stale entries cause readers to see old values. Setting
the TTL too high makes staleness likely, while too low a TTL wastes the cache's
benefit."*

Resulting cards:

```markdown
## 1. 缓存为什么能降低延迟

- **核心知识**：命中缓存时，重复读取由快速内存响应，不必回源。
- **简明解释**：延迟下降来自"少走一次源站读取"这一步，而不是缓存本身更快。
- **例子**：同一份数据被反复读取时，第一次回源、后续命中缓存。

## 2. 缓存失效是正确性问题，不是性能问题

- **核心知识**：底层数据变化时必须让缓存条目失效，否则读者会读到旧值。
- **简明解释**：失效处理的是数据正确性；不做失效，缓存越快错得越久。
- **自测**：如果底层数据已更新但缓存未失效，读者会看到什么？

## 3. TTL 是新鲜度与收益之间的取舍

- **核心知识**：TTL 过高容易读到陈旧数据，过低则失去缓存收益。
- **简明解释**：TTL 不是一个可以单方向调优的参数，两个方向都有代价。
- **自测**：把 TTL 调得很高，会改善什么、又会恶化什么？
```

Note three cards, not eight: the source yields three distinct points. Padding
this to eight would require inventing material.

## Handling short or thin sources

- Very short source (a paragraph or two): produce 1-3 cards and say the source
  was too short to support more. Do not expand it with your own knowledge.
- A list of unrelated tips: each genuinely independent tip can be its own card,
  but drop tips that merely restate one another.
- Mostly narrative or opinion with little reusable knowledge: say so. Offer to
  extract the few defensible points rather than forcing a card set.
- If the source contradicts itself, prefer the more specific or better-supported
  statement and flag the conflict briefly in that card's explanation. Once you
  have rejected a figure, do not reuse it as that card's example: an example
  built on a superseded number undercuts the card. Use the adopted figure, or
  switch that card to a self-test question.

## Resources

This skill has no bundled scripts or assets. It is a prompt-and-rubric skill:
the extraction rules, the field contract, and the quality gate above are the
whole implementation. Do not add helper scripts or a pipeline for it.
