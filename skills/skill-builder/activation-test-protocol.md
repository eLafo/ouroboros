# Activation Test Protocol

Standardized testing procedures to validate that Skills activate reliably and appropriately.

---

## Purpose

Activation testing is CRITICAL because:
- A Skill with poor activation is effectively **invisible** to users
- False activations waste context and frustrate users
- Testing reveals missing keywords that need to be added to the description

**Investment:** Spend 10-15 minutes on activation testing (15-20% of total creation time)

**Success criteria:** ≥80% activation rate on intended triggers, 0% on boundary cases

---

## Test Suite Overview

Run all 6 tests in sequence:

| Test | Purpose | Expected Result |
|------|---------|----------------|
| Test 1: Direct Keyword | Verify explicit keywords activate | ✅ Activates |
| Test 2: Contextual | Verify implicit context activates | ✅ Activates |
| Test 3: File-Based | Verify file extensions activate | ✅ Activates |
| Test 4: Boundary (Negative) | Verify unrelated requests don't activate | ✅ Does NOT activate |
| Test 5: Synonym | Verify paraphrasing activates | ✅ Activates |
| Test 6: Technology Mention | Verify tool names activate | ✅ Activates |

**Minimum to pass:** 5/6 tests (Test 3 may not apply to all Skills)

---

## Test 1: Direct Keyword Activation

**Objective:** Verify Skill activates when user mentions 3+ keywords from description

**Methodology:**

1. Extract key nouns and verbs from your description
2. Construct user request using 3-5 of these keywords
3. Test if Skill activates

**Example for pdf-processor:**

**Description keywords:**
- extract, text, images, tables, PDF, pdfplumber, PyPDF2, tabula, parse, invoices, reports, forms, merge, split, convert, Word, encrypted, scanned, OCR, document, data parsing, invoice processing

**Test request:**
```
User: "Can you extract tables from this quarterly-report.pdf?"
```

**Analysis:**
- Keywords used: extract (verb), tables (noun), report (noun), PDF (file type)
- Keyword count: 4 ✅
- Expected: Skill activates ✅

**How to test:**
1. Start a new Claude Code session (to load Skills fresh)
2. Type the test request
3. Observe if Skill's instructions are loaded
4. Look for Skill name in Claude's reasoning or response

**Pass criteria:** Skill activates when 3+ keywords are mentioned

---

## Test 2: Contextual Activation

**Objective:** Verify Skill activates from context without explicit keyword mentions

**Methodology:**

1. Write a natural request that implies the Skill's domain
2. Use synonyms or related terms instead of exact keywords
3. Provide context (file names, problem description)

**Example for pdf-processor:**

**Test request:**
```
User: "What data is in this invoice?" [attaches invoice.pdf]
```

**Analysis:**
- Direct keywords: invoice (mentioned), data (implied)
- Contextual clues: .pdf file extension, asking about "data in invoice"
- Expected: Skill activates ✅ (invoice processing is in triggers)

**More examples:**

**For api-tester:**
```
User: "This endpoint is returning 500 errors, can you help debug it?"
```
- Contextual: endpoint → API, 500 errors → HTTP status codes, debug → testing
- Expected: ✅ Activates

**For log-analyzer:**
```
User: "I'm seeing crashes in the application logs"
```
- Contextual: crashes → errors, application logs → log files
- Expected: ✅ Activates

**Pass criteria:** Skill activates from contextual clues without requiring exact keyword matches

---

## Test 3: File-Based Activation

**Objective:** Verify Skill activates based on file extensions or file types

**Note:** Only applicable if Skill works with specific file types

**Methodology:**

1. Mention a file with relevant extension
2. Use minimal keywords in request
3. Let file extension trigger activation

**Example for pdf-processor:**

**Test request:**
```
User: "Process this document" [attaches quarterly-report.pdf]
```

**Analysis:**
- Minimal keywords: process, document (generic)
- File extension: .pdf (specific)
- Expected: ✅ Activates based on .pdf extension

**More examples:**

**For json-validator:**
```
User: "Check this file" [attaches config.json]
```
- File extension: .json triggers json-validator
- Expected: ✅ Activates

**For csv-analyzer:**
```
User: "What's in this data?" [attaches sales.csv]
```
- File extension: .csv
- Expected: ✅ Activates

**Pass criteria:** Skill activates when relevant file type is present, even with generic request

**If not applicable:** Mark as N/A (Skill doesn't work with specific file types)

---

## Test 4: Boundary Test (Negative)

**Objective:** Verify Skill does NOT activate for unrelated requests

**This is CRITICAL:** False activations waste context and frustrate users

**Methodology:**

1. Write requests from completely different domains
2. Use keywords that might overlap but context is wrong
3. Ensure Skill remains dormant

**Example for pdf-processor:**

**Test requests (should NOT activate):**

```
User: "Can you test my REST API endpoints?"
Expected: ❌ Does NOT activate (api-tester should activate instead)

User: "Analyze these error logs for patterns"
Expected: ❌ Does NOT activate (log-analyzer should activate instead)

User: "Optimize this SQL query"
Expected: ❌ Does NOT activate (sql-optimizer should activate instead)

User: "Extract data from this CSV file"
Expected: ❌ Does NOT activate (might activate csv-processor if it exists)
```

**Analysis:**
- "extract data" overlaps with pdf-processor keywords
- BUT "CSV file" clearly indicates different domain
- pdf-processor should recognize this is not a PDF task

**More examples:**

**For api-tester (should NOT activate):**
```
User: "Parse this PDF invoice"
Expected: ❌ (pdf-processor should activate)

User: "Analyze application logs"
Expected: ❌ (log-analyzer should activate)
```

**Pass criteria:**
- Skill does NOT activate for 3+ unrelated requests
- Other domain-appropriate Skills may activate instead (this is correct behavior)

**If Skill activates incorrectly:**
1. Description is too broad or generic
2. Need to add domain-specific constraints
3. Remove generic keywords that cause false positives

---

## Test 5: Synonym Test

**Objective:** Verify Skill activates when user paraphrases using synonyms

**Methodology:**

1. Take a working test request (from Test 1)
2. Replace keywords with synonyms or related terms
3. Ensure Skill still activates

**Example for pdf-processor:**

**Original (Test 1):**
```
User: "Can you extract tables from this quarterly-report.pdf?"
→ ✅ Activates
```

**Synonym variants:**

**Variant 1:**
```
User: "Help me parse data from this report document" [attaches report.pdf]
```
- Synonyms: parse ≈ extract, data ≈ tables, report ≈ quarterly-report
- Expected: ✅ Activates

**Variant 2:**
```
User: "I need to pull information from this PDF file"
```
- Synonyms: pull ≈ extract, information ≈ data
- Expected: ✅ Activates

**Variant 3:**
```
User: "Get the table data out of invoice.pdf"
```
- Synonyms: get out ≈ extract
- Expected: ✅ Activates

**More examples:**

**For api-tester:**

Original: "Test my REST API endpoints"

Synonyms:
- "Validate my API service" (validate ≈ test, service ≈ endpoints)
- "Check if my HTTP API works" (check ≈ test, HTTP API ≈ REST API)
- "Debug this REST endpoint" (debug ≈ test)

**Pass criteria:** Skill activates for 2/3 synonym variants

**If failing:**
- Add synonyms to description
- Include related terms in trigger scenarios
- Test with more natural language variations

---

## Test 6: Technology Mention

**Objective:** Verify Skill activates when user mentions specific tools/technologies

**Methodology:**

1. Identify technologies mentioned in description
2. Create requests that explicitly mention these tools
3. Verify activation

**Example for pdf-processor:**

**Technologies in description:** pdfplumber, PyPDF2, tabula, OCR

**Test requests:**

```
User: "I want to use pdfplumber to extract tables"
Expected: ✅ Activates (explicit tool mention)

User: "Can you help me with PyPDF2?"
Expected: ✅ Activates (tool mentioned)

User: "Run OCR on this scanned document"
Expected: ✅ Activates (technology + use case)
```

**More examples:**

**For api-tester:**

Technologies: REST, HTTP, OpenAPI, OAuth

```
User: "Validate this against my OpenAPI spec"
→ ✅ Activates

User: "Test OAuth authentication"
→ ✅ Activates
```

**For sql-optimizer:**

Technologies: PostgreSQL, MySQL, EXPLAIN

```
User: "Analyze this PostgreSQL EXPLAIN output"
→ ✅ Activates
```

**Pass criteria:** Skill activates when 2+ technologies are mentioned

---

## Testing Workflow

### Step 1: Prepare Test Matrix

Create a test matrix before testing:

```markdown
## Activation Test Matrix - [skill-name]

| Test | Request | Keywords | Expected | Result |
|------|---------|----------|----------|--------|
| 1. Direct | "Extract tables from report.pdf" | extract, tables, PDF | ✅ Activate | |
| 2. Contextual | "What's in this invoice?" [invoice.pdf] | invoice, PDF | ✅ Activate | |
| 3. File-based | "Process this" [data.pdf] | .pdf extension | ✅ Activate | |
| 4a. Boundary | "Test my API" | - | ❌ No activate | |
| 4b. Boundary | "Analyze logs" | - | ❌ No activate | |
| 4c. Boundary | "Optimize SQL" | - | ❌ No activate | |
| 5a. Synonym | "Parse data from report.pdf" | parse, data, PDF | ✅ Activate | |
| 5b. Synonym | "Pull info from invoice.pdf" | pull, info, PDF | ✅ Activate | |
| 6a. Tech | "Use pdfplumber to extract" | pdfplumber | ✅ Activate | |
| 6b. Tech | "Help with PyPDF2" | PyPDF2 | ✅ Activate | |

**Success criteria:** ≥8/10 tests pass (80%)
```

### Step 2: Execute Tests

**In a fresh Claude Code session:**

1. Type Test 1 request
2. Observe activation (check Claude's response for Skill loading)
3. Record result in matrix
4. Clear conversation or start new session
5. Repeat for all tests

**Why fresh session?**
- Skills load at session start
- Previous activations can influence future ones
- Clean slate ensures accurate testing

### Step 3: Analyze Results

**If <80% pass rate:**

1. **Identify failure pattern:**
   - Failing on direct keywords? → Description lacks those keywords
   - Failing on contextual? → Need more domain vocabulary
   - Failing on synonyms? → Add synonym keywords
   - False positives (Test 4)? → Description too generic

2. **Revise description:**
   - Add missing keywords
   - Make more specific (if too broad)
   - Add trigger scenarios
   - Include synonyms

3. **Re-test:**
   - Run failed tests again
   - Verify improvements
   - Iterate until ≥80%

### Step 4: Document Results

**In SKILL.md, add activation test results:**

```markdown
## Activation Testing

**Tested:** 2025-11-05

**Test results:**
- Direct keyword activation: ✅ Pass
- Contextual activation: ✅ Pass
- File-based activation: ✅ Pass
- Boundary tests (3 cases): ✅ All passed (no false activations)
- Synonym tests (3 variants): ✅ 3/3 passed
- Technology mentions (2 tests): ✅ 2/2 passed

**Overall: 10/10 tests passed (100%)**

**Example activation phrases:**
- "Extract tables from this PDF invoice"
- "Parse data from quarterly-report.pdf"
- "Use pdfplumber to get tables"
- "What's in this document?" [with .pdf file]
```

---

## Troubleshooting Failed Tests

### Issue 1: Never Activates (0% success)

**Symptoms:** Skill doesn't activate for any test

**Possible causes:**
1. YAML syntax error (Skill didn't load)
2. Directory name ≠ YAML name field
3. Session not restarted after Skill creation

**Diagnosis:**

```bash
# Check YAML is valid
python3 -c "import yaml; yaml.safe_load(open('.claude/skills/skill-name/SKILL.md').read().split('---')[1])"

# Check name consistency
DIRNAME=$(basename .claude/skills/skill-name)
YAMLNAME=$(grep "^name:" .claude/skills/skill-name/SKILL.md | cut -d: -f2 | xargs)
echo "Directory: $DIRNAME"
echo "YAML name: $YAMLNAME"
# Should match exactly
```

**Fixes:**
1. Fix YAML syntax errors
2. Ensure directory name = YAML name field
3. Restart Claude Code session

---

### Issue 2: Low Activation Rate (20-60%)

**Symptoms:** Skill sometimes activates but unreliable

**Possible causes:**
1. Description lacks key trigger keywords
2. Missing common synonyms
3. Not enough technology mentions

**Diagnosis:**

```markdown
Compare description keywords vs. test requests:

Description keywords: [extract, PDF, tables]
Test 1 keywords: [parse, document, data] → No overlap ❌
Test 2 keywords: [extract, invoice, PDF] → 2 overlaps ✅
Test 3 keywords: [get, information, file] → No overlap ❌
```

**Fixes:**
1. Add missing keywords to description
   - If "parse" used in tests, add "parse" to description
2. Include synonyms explicitly
   - "Extract and parse data..."
3. Add more "Use when..." triggers
   - "Use when parsing documents, extracting data, or working with PDFs"

**Example revision:**

Before (50% activation):
```
Extract tables from PDFs using pdfplumber.
```

After (90% activation):
```
Extract and parse text, images, and tables from PDF documents using pdfplumber, PyPDF2, and tabula. Process invoices, reports, and forms. Use when working with PDF files, document extraction, data parsing, or invoice processing.
```

---

### Issue 3: False Activations (Test 4 failures)

**Symptoms:** Skill activates for unrelated requests

**Possible causes:**
1. Description too generic
2. Generic keywords like "data", "files", "process"
3. Missing domain constraints

**Diagnosis:**

```markdown
Test 4 failures:
- "Extract data from CSV" → ❌ Should NOT activate (but did)
- "Process these log files" → ❌ Should NOT activate (but did)

Problem keywords: "extract", "data", "process" are too generic
```

**Fixes:**
1. Make description more specific:
   - "Extract tables from **PDFs**" not "Extract data from files"
2. Add technology constraints:
   - "using pdfplumber, PyPDF2" makes clear it's PDF-specific
3. Remove generic terms:
   - Replace "process" with "extract", "parse", "convert"

**Example revision:**

Before (false activations):
```
Extract and process data from various file formats. Handle tables, text, and images.
```

After (no false activations):
```
Extract text, images, and tables from PDF files using pdfplumber and PyPDF2. Parse PDF invoices, reports, and forms. Use when working with PDF documents.
```

---

### Issue 4: Synonym Tests Fail

**Symptoms:** Works with exact keywords but not paraphrasing

**Possible causes:**
1. Description too literal (only mentions exact terms)
2. Missing common synonyms

**Diagnosis:**

```markdown
Working: "extract tables from PDF"
Failing: "parse data from PDF" (parse ≈ extract, data ≈ tables)
Failing: "pull information from PDF" (pull ≈ extract, information ≈ data)

Missing synonyms: parse, pull, data, information
```

**Fixes:**
1. Add synonyms to description:
   - "Extract and parse" (both verbs)
   - "tables and data" (both nouns)
2. Use varied vocabulary in examples section
3. Include natural language in triggers:
   - "Use when extracting, parsing, or retrieving data from PDFs"

---

## Advanced Testing

### A. Multi-Skill Scenarios

Test how Skill behaves when multiple Skills could apply:

**Scenario:** Both pdf-processor and data-validator could handle "validate this PDF data"

**Expected behavior:**
- Either Skill can activate (both are valid)
- More specific Skill should activate (pdf-processor if PDF focus, data-validator if validation focus)

**Test:**
```
User: "Validate the data in this PDF report"

Could activate:
- pdf-processor (PDF keyword)
- data-validator (validate keyword)

Best activation: pdf-processor (more specific: PDF + data)
```

**If wrong Skill activates:**
- Make descriptions more differentiated
- Add unique keywords to each

---

### B. Edge Case Testing

Test unusual but valid requests:

**For pdf-processor:**
```
User: "My PDF is encrypted, can you extract the tables?"
→ Should activate (encrypted PDFs mentioned in description)

User: "This is a scanned PDF, need OCR"
→ Should activate (scanned + OCR in description)

User: "Merge these 50 PDF files"
→ Should activate (merge PDFs in description)
```

**If edge cases fail:**
- Add edge case keywords to description
- Include in "Use when..." triggers

---

### C. File Path Testing

Test if Skill activates with file paths instead of attachments:

```
User: "Extract data from ./reports/Q3-2024.pdf"
→ Should activate (.pdf in path)

User: "Process all PDFs in ./invoices/"
→ Should activate (PDFs + directory)
```

---

## Quality Metrics

Track activation quality over time:

**Metrics to measure:**
- **Precision:** What % of activations are correct? (Target: >95%)
- **Recall:** What % of relevant requests activate this Skill? (Target: >80%)
- **False Positive Rate:** What % of unrelated requests activate? (Target: <5%)

**Example:**

```markdown
## Activation Metrics - pdf-processor

**Test date:** 2025-11-05
**Tests conducted:** 20 requests

Results:
- Relevant requests: 15
- Activations on relevant: 13 (Recall: 87% ✅)
- Total activations: 14
- Correct activations: 13 (Precision: 93% ✅)
- False positives: 1 (FPR: 5% ✅)

Conclusion: Meets quality targets
```

---

## Checklist

Before marking Skill as complete, verify:

- [ ] Test 1 (Direct keywords) passes
- [ ] Test 2 (Contextual) passes
- [ ] Test 3 (File-based) passes or marked N/A
- [ ] Test 4 (Boundary) passes (no false activations)
- [ ] Test 5 (Synonyms) passes (2/3 variants)
- [ ] Test 6 (Technology) passes (2+ tools)
- [ ] Overall success rate ≥80%
- [ ] Documented test results in SKILL.md
- [ ] If <80%, iterated on description and re-tested

---

**Activation testing is the difference between a useful Skill and an invisible one. Invest the time to get it right!**
