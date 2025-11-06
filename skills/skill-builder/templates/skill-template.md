# Skill Template

Use this template as a starting point for creating new Claude Code Skills. Replace all bracketed placeholders with your Skill-specific content.

---

## Quick Start

1. Copy this template to `.claude/skills/[your-skill-name]/SKILL.md`
2. Replace all `[PLACEHOLDERS]` with your content
3. Follow the validation checklist at the bottom
4. Test activation thoroughly

---

## Template

```markdown
---
name: [skill-name]
description: [Your carefully crafted description following the formula: ACTION VERBS + CAPABILITIES + using TECHNOLOGIES. ADDITIONAL CAPABILITIES. Use when TRIGGER SCENARIOS.]
allowed-tools: [Read, Write, Edit, Bash, Grep, Glob, Task, WebFetch, WebSearch]
---

# [Skill Name]: [Brief Tagline]

[1-2 paragraph overview explaining what this Skill does, why it's useful, and who benefits from it. Be specific about the domain and value proposition.]

## Core Capabilities

- **[Capability 1]:** [Brief description of what this capability does]
- **[Capability 2]:** [Brief description]
- **[Capability 3]:** [Brief description]
- **[Capability 4]:** [Brief description]
- **[Capability 5]:** [Brief description]

## Methodology

### Step 1: [First Major Step]

[Detailed explanation of this step]

**When to use this approach:**
- [Scenario 1]
- [Scenario 2]

**Example:**
```[language]
[code example]
```

### Step 2: [Second Major Step]

[Detailed explanation]

**Decision tree:**
- **If [condition]** → [action]
- **If [condition]** → [action]
- **Otherwise** → [default action]

**Example:**
```[language]
[code example]
```

### Step 3: [Third Major Step]

[Detailed explanation]

**Example:**
```[language]
[code example]
```

### Step 4: [Fourth Major Step]

[Detailed explanation with any special considerations]

**Example:**
```[language]
[code example]
```

## Examples

### Example 1: [Descriptive Scenario Name]

**Scenario:** [Brief description of what user wants to achieve]

**Approach:**
[Explanation of how to solve this]

**Implementation:**
```[language]
[Complete, runnable code example]
```

**Expected output:**
```
[What the user should see]
```

### Example 2: [Second Scenario]

**Scenario:** [Description]

**Approach:**
[Explanation]

**Implementation:**
```[language]
[Complete code]
```

**Expected output:**
```
[Output]
```

### Example 3: [Third Scenario]

**Scenario:** [Description]

**Approach:**
[Explanation]

**Implementation:**
```[language]
[Complete code]
```

**Expected output:**
```
[Output]
```

### Example 4: [Fourth Scenario]

**Scenario:** [Description]

**Approach:**
[Explanation]

**Implementation:**
```[language]
[Complete code]
```

**Expected output:**
```
[Output]
```

### Example 5: [Fifth Scenario]

**Scenario:** [Description]

**Approach:**
[Explanation]

**Implementation:**
```[language]
[Complete code]
```

**Expected output:**
```
[Output]
```

## Common Patterns

### Pattern 1: [Common Use Case Name]

[Description of when to use this pattern]

**Implementation:**
```[language]
[Reusable code pattern]
```

**Variations:**
- **[Variation 1]:** [How to adapt the pattern]
- **[Variation 2]:** [Another adaptation]

### Pattern 2: [Second Common Pattern]

[Description]

**Implementation:**
```[language]
[Code pattern]
```

### Pattern 3: [Third Pattern]

[Description and implementation]

## Troubleshooting

### Issue 1: [Common Problem]

**Symptoms:** [How user knows they have this problem]

**Cause:** [Why this happens]

**Solution:**
```[language]
[Fix code or commands]
```

**Explanation:** [Why this fix works]

### Issue 2: [Second Common Problem]

**Symptoms:** [Description]

**Cause:** [Root cause]

**Solution:**
```[language]
[Fix]
```

**Prevention:** [How to avoid this in the future]

### Issue 3: [Third Problem]

**Symptoms:** [Description]

**Cause:** [Root cause]

**Solution:**
[Step-by-step fix]

### Issue 4: [Fourth Problem]

[Same structure as above]

## Best Practices

### Do's ✅

- ✅ [Best practice 1]
- ✅ [Best practice 2]
- ✅ [Best practice 3]
- ✅ [Best practice 4]
- ✅ [Best practice 5]

### Don'ts ❌

- ❌ [Anti-pattern 1] - [Why to avoid]
- ❌ [Anti-pattern 2] - [Why to avoid]
- ❌ [Anti-pattern 3] - [Why to avoid]
- ❌ [Anti-pattern 4] - [Why to avoid]

## Advanced Usage

### [Advanced Topic 1]

[When users need more sophisticated approaches]

**Example:**
```[language]
[Advanced code example]
```

### [Advanced Topic 2]

[Description and example]

## Related Tools and Skills

**Complementary Skills:**
- **[related-skill-1]:** [When to use instead/together]
- **[related-skill-2]:** [Relationship]

**External tools:**
- **[tool-name]:** [Brief description and installation]
- **[tool-name]:** [Description]

## References

- [Official documentation link]
- [Tutorial or guide link]
- [Additional resource]

---

**Created:** [YYYY-MM-DD]
**Last updated:** [YYYY-MM-DD]
**Status:** Active
```

---

## Template Customization Guide

### Section Priorities

**REQUIRED sections (Skill won't be useful without these):**
1. ✅ YAML frontmatter (name, description, allowed-tools)
2. ✅ Overview paragraph
3. ✅ Core Capabilities list
4. ✅ Methodology (step-by-step)
5. ✅ At least 3 examples
6. ✅ Common Patterns
7. ✅ Troubleshooting

**OPTIONAL sections (include if relevant):**
- Best Practices (if there are common mistakes to avoid)
- Advanced Usage (if Skill has sophisticated capabilities)
- Related Tools (if Skill integrates with others)
- References (if external docs are crucial)

### How to Adapt for Your Domain

**For analysis/research Skills:**
- Emphasize Methodology section (decision trees, approaches)
- Include diverse examples covering edge cases
- Heavy troubleshooting section

**For execution/automation Skills:**
- Focus on Examples section (copy-paste ready code)
- Include Common Patterns for frequent tasks
- Clear Do's and Don'ts

**For validation/checking Skills:**
- Methodology should explain what to check and why
- Examples showing both valid and invalid cases
- Troubleshooting for false positives/negatives

### Content Length Guidelines

**Minimum viable Skill:**
- Overview: 100-200 words
- Methodology: 3-5 steps with examples
- Examples: 3 complete scenarios
- Common Patterns: 2-3 patterns
- Troubleshooting: 3-4 issues
- **Total:** ~1500-2500 words

**Comprehensive Skill:**
- Overview: 200-400 words
- Methodology: 5-8 steps with decision trees
- Examples: 5-7 scenarios
- Common Patterns: 4-6 patterns
- Troubleshooting: 5-8 issues
- Best Practices: Included
- Advanced Usage: 2-3 topics
- **Total:** ~3500-5000 words

**Don't exceed:** 8000 words (diminishing returns, harder to maintain)

---

## Description Formula Reference

Use this proven formula for the description field:

```
[ACTION VERBS] [CAPABILITIES] using [TECHNOLOGIES/TOOLS].
[ADDITIONAL CAPABILITIES].
Use when [TRIGGER SCENARIOS].
```

**Components:**

1. **Action Verbs (5+ verbs):**
   - Extract, analyze, test, validate, generate, optimize, convert, parse, debug, detect, compare, merge, split, etc.

2. **Capabilities (specific, not vague):**
   - What specific operations does the Skill perform?
   - Mention different modes or approaches if applicable

3. **Technologies/Tools (3+ mentions):**
   - File types: PDF, JSON, CSV, XML
   - Frameworks: REST, OpenAPI, SQL, GraphQL
   - Tools: pdfplumber, pytest, eslint, jq
   - Platforms: PostgreSQL, AWS, Docker

4. **Trigger Scenarios (10+ keywords):**
   - "Use when working with [X]"
   - "Use for [Y] tasks"
   - "Use when [Z] situations"

**Examples:**

**Good description:**
```
Extract text, images, and tables from PDFs using pdfplumber, PyPDF2, and tabula. Parse invoices, reports, and forms. Merge multiple PDFs, split pages, convert between PDF and Word formats. Handle encrypted PDFs and scanned documents with OCR. Use when working with PDF files, document extraction, data parsing, or invoice processing tasks.
```

**Why it's good:**
- ✅ 6 action verbs (extract, parse, merge, split, convert, handle)
- ✅ Specific capabilities (text/images/tables, merge, split, OCR)
- ✅ 6 technologies (pdfplumber, PyPDF2, tabula, PDF, Word, OCR)
- ✅ 10+ trigger keywords (PDF, document, extraction, parsing, invoice, reports, forms, etc.)

---

## Validation Checklist

Before considering your Skill complete, verify:

### YAML Frontmatter
- [ ] Opening `---` on line 1
- [ ] Closing `---` after all fields
- [ ] `name` field matches directory name exactly
- [ ] `description` is under 1024 characters
- [ ] `description` passes quality checklist (see below)
- [ ] `allowed-tools` uses comma-separated format (not YAML array)
- [ ] No tab characters (spaces only)
- [ ] No smart quotes (straight quotes only)
- [ ] Valid YAML (test with: `python3 -c "import yaml; yaml.safe_load(...)"`)

### Description Quality
- [ ] Includes 5+ action verbs
- [ ] Lists specific capabilities (not vague like "helps with")
- [ ] Mentions 3+ technologies/file types/frameworks
- [ ] Includes "Use when/for..." trigger scenarios
- [ ] Contains 10+ keywords users will naturally mention
- [ ] Reads naturally (not keyword-stuffed)
- [ ] 50-200 words total
- [ ] Under 1024 characters (hard limit)
- [ ] No vague terms ("various", "multiple", "helps", etc.)
- [ ] Passes "would I tell a colleague this?" test

### Content Quality
- [ ] Overview clearly explains purpose and value
- [ ] Core Capabilities list is specific and actionable
- [ ] Methodology has step-by-step instructions
- [ ] At least 3 complete examples (recommend 5)
- [ ] Examples are runnable (no placeholders or TODOs)
- [ ] Examples cover different scenarios/use cases
- [ ] Common Patterns section with reusable code
- [ ] Troubleshooting section with 3+ issues
- [ ] All code blocks have language specified
- [ ] No Lorem Ipsum or placeholder text

### Activation Testing
- [ ] Test 1: Direct keyword activation (passes)
- [ ] Test 2: Contextual activation (passes)
- [ ] Test 3: File-based activation if applicable (passes)
- [ ] Test 4: Boundary test - should NOT activate (passes)
- [ ] Test 5: Synonym test with paraphrasing (passes)
- [ ] Activation success rate >80% on intended triggers

### Documentation
- [ ] Added to project README
- [ ] Example usage documented
- [ ] Committed to git (if project Skill)
- [ ] Team notified (if team-shared Skill)

---

## Example: Filled Template (Abbreviated)

Here's how a completed Skill looks:

```markdown
---
name: json-validator
description: Validate JSON files against JSON Schema, check data types, find missing required fields, detect invalid formats. Support custom validation rules, generate detailed error reports with line numbers. Use when validating JSON data, checking API responses, ensuring data integrity, or working with JSON Schema.
allowed-tools: Read, Bash, Write
---

# JSON Validator: Data Quality Assurance

Validate JSON files against JSON Schema specifications and custom rules. Ensure data integrity before imports, API consumption, or processing pipelines.

## Core Capabilities

- **Schema Validation:** Check JSON against JSON Schema (draft-07)
- **Type Checking:** Verify data types match expectations
- **Required Fields:** Detect missing required properties
- **Format Validation:** Validate emails, dates, URLs, UUIDs
- **Custom Rules:** Apply domain-specific validation logic
- **Error Reporting:** Generate detailed reports with line numbers

## Methodology

### Step 1: Load JSON and Schema

[Detailed instructions with code examples...]

[Continue with all sections following the template...]
```

---

## Supporting Files Template

If your Skill is complex, consider creating additional files:

**examples.md** - Extended examples beyond SKILL.md
**reference.md** - Detailed API/command reference
**templates/** - Reusable code templates or config files

---

**This template accelerates Skill creation by providing proven structure. Customize sections based on your Skill's domain and complexity.**
