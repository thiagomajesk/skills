---
name: imprint
description: Extracts author's tone from content into a reusable skill. Should be used when you want to write in the same tone as the original content. Use this skill to create a tone profile that can be applied to future writing tasks, ensuring consistency in style and voice.
---

# Overview

**Task**: Analyze texts by the same author, extract the patterns that define their voice, and turn those patterns into a reusable writing skill.

**Inputs**: Accept source material as raw text or URLs. If given URLs, fetch and extract the relevant content before analysis. If the source scope is unclear, ask which texts or author pages to use.

## Distillation

Reduce the author's voice to clear, reusable rules that another agent can apply when writing new content. Each rule should guide concrete choices in tone, structure, rhythm, diction, and argumentation, and should be grounded in recurring patterns from the source material.

Focus on patterns that transfer across topics and formats. Do not mistake subject matter for voice: repeated topics, domains, tools, or references are not stylistic traits by themselves. Do not mistake formatting for voice: headings, bullets, blockquotes, code fences, and other markdown or layout choices should only be included when they function as recurring rhetorical moves, not merely as formatting habits.

## Identity

Identify the traits that make this author's writing recognizable at a glance. Focus on the patterns that are most diagnostic across multiple samples, and describe them in a way that can guide new writing rather than merely label it. Things to consider include:

- Formality level
- Paragraph pacing
- Rhetorical structure
- Emotional temperature
- Sentence rhythm and length
- Diction and vocabulary level
- Amount of precision vs flourish
- Preferred structure of arguments
- Patterns that would break authenticity
- Typical openings, transitions, and conclusions

### Stylistic Devices

Identify only the stylistic devices that recur often enough to shape the author's voice. For each one, note how often it appears, what effect it creates, and where it tends to appear in the piece. Focus on devices that are useful for reproducing the voice in new writing. Ignore incidental or one-off devices that do not meaningfully affect the author's style.

### Anti-Patterns

Identify the patterns that would make the writing feel unlike this author. Include both surface-level mismatches and deeper violations of stance, structure, or judgment. Examples include:

- Tones that are too exaggerated
- Habits that would feel out of character
- Rhetorical moves the author does not use
- Habits that are intentionally broken for effect.
- Patterns that are not followed in certain contexts.
- Stylistic clichés that would turn the output into parody
- Common mistakes that the author would not make due to their expertise

## Process

1. Gather representative samples from the same author. If the input is a URL, extract the relevant text first.
2. Analyze the samples to identify recurring patterns in tone, diction, rhythm, structure, stance, and pacing. 
3. Convert those patterns into a reusable skill with clear rules, guardrails, and a list of clear examples.
4. Test the skill on new prompts and revise it when the output misses the author's voice in repeatable ways.

### Notes

- Separate durable habits from one-off quirks.
- Prefer fresh evaluators during testing to reduce bias.
- Separate durable voice traits from formatting artifacts.
- Use parallel or adversarial reviewers only when they improve coverage or validation.
- Update the existing `prose` skill if it already exists instead of creating a duplicate.

## Skill

Use the template below to create the final `SKILL.md`. Follow the document structure strictly without deviation. Ensure that every section is written in a clear and concise manner, with a focus on providing actionable guidance for writing in the author's voice. 

```markdown
---
name: prose
description: Write in {first name}'s voice. Use this skill when drafting content that should read as if written by this author.
---

# Summary

A short paragraph naming the traits that make this voice recognizable.

## Profile

Describe only transferable voice traits. Do not encode topic preferences or formatting habits unless they clearly shape the voice across multiple samples and would still matter in a different subject or format.

**Tone and Stance:** Describe the emotional temperature, level of formality, relationship to the reader, and how the author positions themselves relative to the subject.

**Rhythm and Diction:** Describe sentence length, cadence, punctuation habits, vocabulary level, and recurring word choices. Include a few phrases that fit the voice and a few that do not.

**Structure and Habits:** Describe how the author usually builds a piece and the recurring moves they use while doing so. Include typical openings, sequencing, transitions, endings, and repeated rhetorical patterns that define the voice.

## Writing Rules

A list of concrete, testable rules. Each rule should be specific enough to guide writing and revision. Write this section as a numbered list. Each rule must begin with a bold imperative, followed by the "why" behind the rule and a "fails if" statement that describes what would break the voice. 

<format>
1. **Rule.**
   *Why:* What pattern it preserves.
   *Fails if:* What breaks the voice.
   *Example:* Optional, only when it clarifies the rule.
</format>

## Anti-Patterns

List the patterns that break authenticity in this voice. Include both surface mismatches and deeper violations of tone, structure, or judgment. Write this section as a flat bullet list. Each bullet must begin with a bold prohibition or constraint, followed by a short explanation and, when useful, one or two concrete examples. Every bullet should be a direct guardrail the writer can follow.

<format>
- **Never ...** Brief explanation of why it breaks the voice.
  *Example:* Optional, only when it clarifies the constraint.
</format>

## Calibration

Provide a list of a few examples demonstrating how to apply the writing rules in a way that fits the author's voice. Each example should include a DO's and DON'Ts to illustrate the application of the skill in a clear and practical manner.

<format>
1. **Example 1**
   - **DO:** A short passage that follows the writing rules and captures the author's voice.
   - **DON'T:** A short passage that violates one or more of the writing rules, demonstrating what breaks the voice.
</format>
```
