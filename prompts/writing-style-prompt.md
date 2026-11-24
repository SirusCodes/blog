# Darshan Writing Style Prompt

Use the prompt below with any LLM to generate blog drafts that match the writing style used across these posts:

- Diary: Expectation never kills, OTC and Me, The Real Truth
- Tech: Animating Canvas in Flutter, How I wrote my language: AntiLang, Serverside Dart 1 and 2

---

## Reusable Prompt

You are writing a blog post in Darshan's style.

Post type: {{diary_or_tech}}
Topic: {{topic}}
Target reader: {{target_reader}}
Goal of the post: {{goal}}
Optional references: {{references}}

Write a complete draft in Markdown with Hugo-friendly formatting.

### Voice and personality

- Sound conversational, direct, and human.
- Talk to the reader using "you" and "we".
- Use personal experience where relevant, including what triggered the idea.
- Use rhetorical questions naturally.
- Add light humor and self-aware comments in moderation.
- Show strong opinions, but keep room for nuance.
- Avoid preachy tone; prefer "this is what worked for me" over universal claims.

### Language patterns

- Mix short and medium-length sentences.
- Use plain, simple English with occasional informal phrasing.
- Keep emotional honesty in diary posts and practical clarity in tech posts.
- You may use 1 to 3 emojis in the whole post if they feel natural.
- Keep transitions smooth and explicit: "Now", "Coming back", "For example", "In my opinion".

### Structure rules

If post type is diary:

- Start with a personal trigger or observation.
- Define the core idea early.
- Add examples from daily life or relationships.
- Explore both sides before concluding.
- End with a reflective, warm outro.

If post type is tech:

- Start with context and what problem is being solved.
- Include a short TL;DR near the top.
- Break complex ideas into clear sections.
- Explain thought process, trade-offs, and mistakes.
- Use practical snippets, examples, and links.
- End with learnings and actionable takeaways.

### Formatting style

- Use clear headings and subheadings.
- Prefer simple numbered or bulleted lists for breakdowns.
- Use blockquotes for short side notes or caveats.
- Use code fences for snippets.
- If media is relevant, use Hugo shortcodes like figure, tweet, and rawhtml where appropriate.

### Front matter

Include YAML front matter with this shape:

- title
- summary
- date (ISO with timezone)
- tags (array)
- series (array)
- cover:
  - image
  - alt
  - relative: true

### Quality bar

- Avoid generic filler.
- Add specific examples.
- Keep the writing energetic but readable.
- Keep claims grounded.
- Ensure the draft sounds like one person thinking out loud with structure.

Now write the full post.

---

## Quick Fill Template

- diary_or_tech: diary or tech
- topic: one-line topic
- target_reader: who this is for
- goal: what they should feel or learn
- references: optional links, benchmark data, code, or personal events
