# Description Patterns and Formulas

Proven patterns and formulas for crafting effective Skill descriptions that activate reliably.

---

## The Master Formula

```
[ACTION VERBS] [CAPABILITIES] using [TECHNOLOGIES/TOOLS].
[ADDITIONAL CAPABILITIES].
Use when [TRIGGER SCENARIOS].
```

**Why this works:**
1. **Action verbs** match user's natural language ("extract", "test", "analyze")
2. **Capabilities** describe what users want to achieve
3. **Technologies** trigger on explicit tool mentions
4. **Trigger scenarios** capture use case keywords

---

## Component Breakdown

### 1. Action Verbs (5+ required)

**Categories:**

**Analysis & Research:**
- Analyze, examine, investigate, inspect, review, audit, assess, evaluate, diagnose

**Data Extraction:**
- Extract, parse, scrape, retrieve, collect, gather, harvest, mine

**Transformation:**
- Convert, transform, translate, compile, transpile, migrate, port

**Generation:**
- Generate, create, build, produce, synthesize, construct, compose

**Validation:**
- Validate, verify, check, test, confirm, ensure, assert, prove

**Optimization:**
- Optimize, improve, enhance, refactor, streamline, tune, boost

**Manipulation:**
- Merge, split, combine, separate, join, divide, aggregate

**Detection:**
- Detect, find, discover, identify, locate, spot, recognize

**Comparison:**
- Compare, diff, match, contrast, correlate, align

**Processing:**
- Process, handle, manage, organize, structure, format

**Example usage in descriptions:**

✅ **Good** (6+ verbs):
```
Extract, parse, and validate JSON data. Convert between formats, merge multiple files, detect schema violations.
```

❌ **Bad** (1-2 verbs):
```
Helps with JSON files.
```

---

### 2. Capabilities (Specific, not vague)

**Specificity levels:**

**❌ Vague (avoid):**
- "Work with files"
- "Handle data"
- "Process documents"
- "Helps with APIs"
- "Various operations"

**⚠️ Generic (acceptable but weak):**
- "Extract data from files"
- "Test API endpoints"
- "Analyze log files"

**✅ Specific (best):**
- "Extract tables, text, and images from PDF invoices"
- "Test REST API endpoints with authentication validation"
- "Analyze application logs for error patterns and stack traces"

**Formula for specificity:**
```
[ACTION] [SPECIFIC OBJECTS] [SPECIFIC CONTEXT/PURPOSE]
```

**Examples:**

| Vague | Specific |
|-------|----------|
| "Process PDFs" | "Extract tables from PDF invoices using tabula" |
| "Test APIs" | "Send HTTP requests and validate JSON responses against OpenAPI specs" |
| "Check code" | "Detect security vulnerabilities like SQL injection and XSS in Python code" |
| "Handle dates" | "Parse dates in multiple formats (ISO 8601, Unix timestamps, human-readable)" |

---

### 3. Technologies/Tools (3+ required)

**Categories to include:**

**File Types:**
- PDF, JSON, CSV, XML, YAML, Markdown, Excel (.xlsx), SQL, HTML

**Frameworks/Protocols:**
- REST, GraphQL, OpenAPI, HTTP, WebSocket, gRPC
- SQL, PostgreSQL, MySQL, MongoDB

**Programming Languages:**
- Python, JavaScript, TypeScript, Go, Rust, Java

**Tools/Libraries:**
- pdfplumber, pytest, eslint, jq, pandas, numpy
- curl, httpie, git, docker

**Platforms:**
- AWS, GitHub, GitLab, Jenkins, Docker, Kubernetes

**Standards:**
- JSON Schema, XML Schema, OpenAPI Specification, RFC standards

**Why this matters:**
Users often mention technologies explicitly:
- "Can you test my **OpenAPI** endpoint?"
- "Extract data from this **PDF** using **pdfplumber**"
- "Validate this **JSON Schema**"

**Examples:**

✅ **Good** (6 tech mentions):
```
Extract text, images, and tables from PDFs using pdfplumber, PyPDF2, and tabula. Convert between PDF and Word formats. Handle encrypted PDFs.
```

❌ **Bad** (0-1 tech mentions):
```
Extract data from documents and convert formats.
```

---

### 4. Trigger Scenarios (10+ keywords)

**Patterns:**

**Pattern A: "Use when [gerund]"**
```
Use when working with PDF files
Use when testing API endpoints
Use when analyzing log files
Use when optimizing SQL queries
```

**Pattern B: "Use for [noun phrase]"**
```
Use for API development
Use for data validation
Use for security audits
Use for performance optimization
```

**Pattern C: "Use when [verb phrase]"**
```
Use when debugging applications
Use when validating data integrity
Use when investigating incidents
Use when troubleshooting errors
```

**Combine multiple patterns:**
```
Use when working with PDF files, document extraction, data parsing, or invoice processing tasks. Use for report generation and archival.
```

**Keyword density:**
Aim for 10+ unique keywords that users will naturally mention:

**Example (PDF processor):**
1. PDF
2. document
3. extraction
4. data parsing
5. invoice
6. reports
7. forms
8. merge
9. convert
10. Word
11. scanned
12. OCR

---

## Proven Patterns by Domain

### Pattern 1: Data Processing Skills

```
[Extract|Parse|Process] [data types] from [file formats] using [tools].
[Additional operations like convert, merge, validate].
Handle [edge cases or formats].
Use when working with [file type], [use case 1], [use case 2], or [use case 3].
```

**Example:**
```
Extract text, images, and tables from PDFs using pdfplumber, PyPDF2, and tabula. Parse invoices, reports, and forms. Merge multiple PDFs, split pages, convert between PDF and Word formats. Handle encrypted PDFs and scanned documents with OCR. Use when working with PDF files, document extraction, data parsing, or invoice processing tasks.
```

**Characteristics:**
- Heavy focus on data types and formats
- Mentions specific tools
- Lists edge cases (encrypted, scanned)
- Multiple trigger scenarios

---

### Pattern 2: Testing/Validation Skills

```
[Test|Validate|Check] [system/data] by [methods].
[Validate against specs or standards].
[Handle authentication or edge cases].
Generate [reports or outputs].
Use for [use case 1], [use case 2], or [use case 3].
```

**Example:**
```
Test REST APIs by sending HTTP requests (GET, POST, PUT, DELETE), validating responses, checking status codes, headers, and JSON payloads. Compare responses against OpenAPI specifications, test authentication (API keys, Bearer tokens, OAuth), handle rate limits. Generate test reports with request/response logs. Use for API development, integration testing, debugging HTTP services, or validating API endpoints.
```

**Characteristics:**
- Emphasizes validation methods
- Mentions standards (OpenAPI)
- Covers authentication scenarios
- Clear output (reports)

---

### Pattern 3: Analysis Skills

```
Analyze [subject] for [issues/patterns].
[Parse/Process] [formats], [filter by criteria], [extract specific data].
[Detect or identify] [patterns or problems].
Generate [statistics or insights].
Use when [debugging/investigating/analyzing], or [troubleshooting specific issues].
```

**Example:**
```
Analyze application logs for errors, warnings, patterns, and anomalies. Parse various log formats (plain text, JSON, syslog), filter by severity levels, extract stack traces, detect recurring patterns. Generate statistics (error frequency, timeline analysis). Search logs with regex, correlate events across multiple log files. Use when debugging applications, investigating incidents, analyzing log files, or troubleshooting errors.
```

**Characteristics:**
- Focus on finding insights
- Multiple input formats
- Statistical/analytical outputs
- Debugging context

---

### Pattern 4: Optimization Skills

```
Optimize [subject] by [methods].
[Analyze/Identify] [problems or inefficiencies].
[Recommend or suggest] [improvements].
Support [technologies or platforms].
Use when [optimizing/improving/enhancing] [subject], or [analyzing performance].
```

**Example:**
```
Optimize SQL queries by analyzing execution plans, suggesting indexes, identifying N+1 queries, and recommending query rewrites. Support PostgreSQL, MySQL, SQLite. Detect performance anti-patterns (SELECT *, missing indexes, inefficient JOINs). Provide EXPLAIN analysis with optimization recommendations. Use when optimizing database queries, improving query performance, analyzing slow queries, or designing database indexes.
```

**Characteristics:**
- Analysis + recommendations
- Platform/technology specific
- Anti-pattern detection
- Performance focus

---

### Pattern 5: Generation/Creation Skills

```
Generate [outputs] from [inputs] using [methodologies].
[Create/Build/Produce] [artifacts] following [standards or patterns].
Support [formats or frameworks].
Use when [creating/generating/building] [outputs], or [working with related inputs].
```

**Example:**
```
Generate comprehensive test suites for Python code using pytest. Create unit tests, integration tests, and fixtures from existing code. Follow best practices (AAA pattern, mocking, parametrization). Generate test data and edge cases. Support Flask, Django, FastAPI frameworks. Use when writing tests, generating test coverage, creating test fixtures, or ensuring code quality.
```

**Characteristics:**
- Input → Output transformation
- Methodology/standards mentioned
- Framework support
- Quality/best practices focus

---

### Pattern 6: Conversion/Migration Skills

```
Convert [format A] to [format B] while [preserving characteristics].
Migrate [source] to [target], handling [edge cases].
Support [multiple formats or versions].
Use when [converting/migrating/porting], [working with format A or B].
```

**Example:**
```
Convert between data formats (JSON, YAML, XML, CSV, TOML) while preserving structure and types. Handle nested objects, arrays, special characters, and encoding issues. Support schema migration and validation. Generate conversion reports for complex transformations. Use when converting configuration files, migrating data formats, working with JSON, YAML, or XML, or ensuring data compatibility.
```

**Characteristics:**
- Format pairs clearly stated
- Preservation guarantees
- Edge case handling
- Multiple format support

---

## Anti-Patterns to Avoid

### ❌ Anti-Pattern 1: Too Vague

**Bad:**
```
Helps with PDF documents and various file operations.
```

**Why it fails:**
- Only 1 weak verb ("helps")
- "various" is vague
- No technologies mentioned
- No trigger keywords
- No "Use when..." scenarios

**Fix:**
```
Extract text, tables, and images from PDF files using pdfplumber and PyPDF2. Merge PDFs, split pages, convert to Word. Use when working with PDF documents, data extraction, or invoice processing.
```

---

### ❌ Anti-Pattern 2: Too Narrow

**Bad:**
```
Extract tables from PDF invoices using tabula-py on Ubuntu Linux.
```

**Why it fails:**
- Too specific (tabula-py only, Ubuntu only)
- Misses other extraction types (text, images)
- Won't activate for general PDF tasks
- Platform restriction unnecessary

**Fix:**
```
Extract text, tables, and images from PDFs using pdfplumber, PyPDF2, and tabula. Parse invoices, reports, and forms. Use when working with PDF files or document extraction.
```

---

### ❌ Anti-Pattern 3: Keyword Stuffing

**Bad:**
```
PDF PDFs document documents file files extract extraction data parse parsing table tables image images text convert conversion Word Excel CSV JSON merge split OCR scan invoice report form.
```

**Why it fails:**
- Unnatural reading
- No sentence structure
- No actual capabilities described
- Fails "tell a colleague" test

**Fix:**
```
Extract text, images, and tables from PDFs using pdfplumber and PyPDF2. Convert between PDF and Word formats. Parse invoices, reports, and forms. Use when working with PDF files or document extraction.
```

---

### ❌ Anti-Pattern 4: Missing Technologies

**Bad:**
```
Test APIs by sending requests and checking responses. Validate data and handle authentication.
```

**Why it fails:**
- No technologies (REST? GraphQL? SOAP?)
- No tools (curl? httpie?)
- No standards (OpenAPI?)
- Generic "APIs" won't differentiate from other Skills

**Fix:**
```
Test REST APIs using HTTP requests, validate JSON responses against OpenAPI specs. Check status codes, headers, payloads. Test authentication (API keys, OAuth). Use for API testing and debugging HTTP services.
```

---

### ❌ Anti-Pattern 5: No Triggers

**Bad:**
```
Extract data from PDFs, convert formats, handle tables and images using various Python libraries.
```

**Why it fails:**
- No "Use when..." clause
- User might not know when to invoke this
- Missing context keywords (invoice, report, form)

**Fix:**
```
Extract data from PDFs, convert formats, handle tables and images using pdfplumber and PyPDF2. Use when working with PDF files, document extraction, invoice processing, or data parsing tasks.
```

---

## Character Limit Strategies

**Hard limit:** 1024 characters (YAML specification)
**Recommended:** 400-700 characters (sweet spot for activation without bloat)

**If you exceed 1024 characters:**

### Strategy 1: Remove Redundancy

**Before (1087 chars):**
```
Analyze SQL queries and optimize database performance by examining execution plans, identifying slow queries, suggesting index improvements, detecting N+1 query problems, recommending query rewrites for better performance, and identifying performance anti-patterns. Support PostgreSQL database, MySQL database, and SQLite database. Analyze EXPLAIN output, EXPLAIN ANALYZE results, and query execution plans. Detect missing indexes, inefficient JOIN operations, suboptimal WHERE clauses, and SELECT * anti-patterns. Use when optimizing database queries, improving SQL query performance, analyzing slow database queries, designing database indexes, or troubleshooting database performance issues.
```

**After (586 chars):**
```
Optimize SQL queries by analyzing execution plans, suggesting indexes, identifying N+1 queries, and recommending query rewrites. Support PostgreSQL, MySQL, SQLite. Detect performance anti-patterns (SELECT *, missing indexes, inefficient JOINs). Provide EXPLAIN analysis with optimization recommendations. Use when optimizing database queries, improving query performance, analyzing slow queries, or designing database indexes.
```

**Changes:**
- "SQL queries and optimize database performance" → "SQL queries"
- "PostgreSQL database, MySQL database" → "PostgreSQL, MySQL"
- "EXPLAIN output, EXPLAIN ANALYZE results, and query execution plans" → "EXPLAIN analysis"
- Removed redundant "database" and "query performance"

### Strategy 2: Consolidate Technologies

**Before:**
```
...using pdfplumber, PyPDF2, tabula-py, pdf2image, pytesseract, and pdf2docx...
```

**After:**
```
...using pdfplumber, PyPDF2, and tabula...
```

**Rationale:** Mention 3-4 key tools, not exhaustive list

### Strategy 3: Combine Capabilities

**Before:**
```
Extract text from PDFs. Extract images from PDFs. Extract tables from PDFs. Merge PDFs. Split PDFs. Convert PDFs to Word. Convert Word to PDFs.
```

**After:**
```
Extract text, images, and tables from PDFs. Merge, split, convert between PDF and Word formats.
```

---

## Quality Checklist

Use this checklist to score your description (need 8/10):

1. [ ] **5+ action verbs** - Extract, parse, test, validate, etc.
2. [ ] **Specific capabilities** - No vague "helps with" or "various"
3. [ ] **3+ technologies** - Tools, frameworks, file types
4. [ ] **"Use when/for..." triggers** - At least one trigger clause
5. [ ] **10+ trigger keywords** - Words users will naturally mention
6. [ ] **Natural reading** - Passes "tell a colleague" test
7. [ ] **50-200 words** - Not too short or long
8. [ ] **Under 1024 characters** - Hard YAML limit
9. [ ] **No vague terms** - Avoid "various", "multiple", "helps"
10. [ ] **Domain-specific** - Not generic "file processing"

**Scoring:**
- 10/10: A+ description, will activate reliably
- 8-9/10: Good, proceed with Skill creation
- 6-7/10: Needs revision before proceeding
- <6/10: Start over with the formula

---

## Testing Your Description

Before writing the full Skill, test if description will activate:

### Test 1: Keyword Extraction

Extract all nouns and verbs from your description:

**Example:**
```
Description: "Extract text, images, and tables from PDFs using pdfplumber. Parse invoices, reports. Use when working with PDF files or document extraction."

Keywords: extract, text, images, tables, PDF, pdfplumber, parse, invoices, reports, working, files, document, extraction
```

**Question:** Would a user naturally use 3+ of these keywords when needing this Skill?

✅ **Yes:** "Can you extract tables from this PDF invoice?" (extract, tables, PDF, invoice = 4 keywords)

### Test 2: Simulated User Requests

Write 5 example user requests and check if they contain keywords:

1. "Help me extract data from this PDF report" → ✅ (extract, PDF, report)
2. "Parse this invoice.pdf into CSV" → ✅ (parse, invoice, PDF)
3. "Can you get the tables from quarterly-report.pdf?" → ✅ (tables, PDF, report)
4. "I need to process this scanned document" → ⚠️ (document, but missing PDF - might not activate)
5. "Extract text using pdfplumber" → ✅ (extract, text, pdfplumber)

**Score: 4/5 clear activations** → Good description

### Test 3: Boundary Cases

Write requests that should NOT activate this Skill:

1. "Test my REST API endpoints" → Should NOT activate ✅
2. "Analyze these log files for errors" → Should NOT activate ✅
3. "Optimize this SQL query" → Should NOT activate ✅

**If ANY of these activate, description is too broad!**

---

## Iteration Examples

See how descriptions improve through iteration:

### Example 1: PDF Processor

**Draft 1 (Score: 3/10):**
```
Helps with PDF documents.
```
- ❌ 1 vague verb ("helps")
- ❌ No specific capabilities
- ❌ No technologies
- ❌ No triggers
- ❌ Only 4 words

**Draft 2 (Score: 6/10):**
```
Extract data from PDF files and convert formats. Work with tables and images.
```
- ✅ 3 verbs (extract, convert, work)
- ⚠️ Somewhat specific (data, tables, images)
- ⚠️ Only 1 technology (PDF)
- ❌ No "Use when..."
- ❌ Only 7 keywords

**Draft 3 (Score: 9/10):**
```
Extract text, images, and tables from PDFs using pdfplumber and PyPDF2. Parse invoices, reports, and forms. Merge PDFs, split pages, convert to Word. Use when working with PDF files or document extraction.
```
- ✅ 6 verbs (extract, parse, merge, split, convert, working)
- ✅ Specific capabilities (text/images/tables, merge, split)
- ✅ 4 technologies (PDF, pdfplumber, PyPDF2, Word)
- ✅ "Use when..." included
- ✅ 12+ keywords

**Final (Score: 10/10):**
```
Extract text, images, and tables from PDFs using pdfplumber, PyPDF2, and tabula. Parse invoices, reports, and forms. Merge multiple PDFs, split pages, convert between PDF and Word formats. Handle encrypted PDFs and scanned documents with OCR. Use when working with PDF files, document extraction, data parsing, or invoice processing tasks.
```
- ✅ Added edge cases (encrypted, scanned, OCR)
- ✅ More specific (multiple PDFs, between formats)
- ✅ Additional trigger (data parsing, invoice processing)

---

### Example 2: API Tester

**Draft 1 (Score: 4/10):**
```
Test APIs and check responses.
```

**Draft 2 (Score: 7/10):**
```
Test REST APIs by sending HTTP requests and validating responses. Check status codes, headers, and JSON data.
```

**Final (Score: 10/10):**
```
Test REST APIs by sending HTTP requests (GET, POST, PUT, DELETE), validating responses, checking status codes, headers, and JSON payloads. Compare responses against OpenAPI specifications, test authentication (API keys, Bearer tokens, OAuth), handle rate limits. Generate test reports with request/response logs. Use for API development, integration testing, debugging HTTP services, or validating API endpoints.
```

---

## Quick Reference Card

**Minimum requirements:**
- ✅ 5+ action verbs
- ✅ 3+ technologies
- ✅ 10+ trigger keywords
- ✅ "Use when/for..." clause
- ✅ Under 1024 characters
- ✅ No vague terms

**Formula:**
```
[VERBS] [CAPABILITIES] using [TECH]. [MORE CAPABILITIES]. Use when [TRIGGERS].
```

**Test:**
1. Extract keywords
2. Write 5 user requests
3. Check boundary cases
4. Score against checklist (need 8/10)

---

**Use these patterns as starting points, then customize for your specific domain. Iterate 2-4 times until you achieve 8/10 or higher on the quality checklist.**
