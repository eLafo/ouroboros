---
description: Guide creation of Claude Code Skills with step-by-step workflows, description crafting, YAML generation, and activation testing. Help write effective descriptions with trigger keywords, validate configurations, and test activation patterns.
argument-hint: [skill-name]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Skill Builder

Guide users through creating Claude Code Skills with emphasis on crafting descriptions that activate reliably.

## Objective

Help users create effective Claude Code Skills that:
1. Activate automatically based on context
2. Have well-crafted descriptions with trigger keywords
3. Use appropriate tool restrictions
4. Follow best practices
5. Work reliably in production

## Usage

**With skill name:**
```bash
/build-skill pdf-processor
```

**General skill creation:**
```bash
/build-skill
```

## What Are Skills?

**Skills** are model-invoked AI capabilities that Claude automatically activates based on contextual relevance. They enable Claude to perform specialized workflows by loading domain-specific instructions, methodologies, and reference materials.

**Key characteristics:**
- **Model-invoked**: Claude decides when to use them (not user-triggered)
- **Context-aware**: Loaded only when needed (progressive disclosure)
- **Extensible**: Can include supporting files (docs, templates, scripts)
- **Scoped**: Can restrict tool access for security

**When to use Skills:**
- Specialized domain knowledge (PDF processing, API testing, SQL optimization)
- Automatic guidance for specific tasks
- Complex workflows requiring methodology
- Tasks users will describe naturally without explicit commands

**When NOT to use Skills:**
- User-triggered explicit workflows (use Commands instead)
- Tasks requiring isolation (use Subagents instead)
- Event-driven automation (use Hooks instead)

---

## Skill Creation Workflow

Follow this proven 8-step workflow:

### Step 1: Planning (5-10 min)

**Gather requirements:**

Ask the user:

1. **What domain or capability will this Skill provide?**
   Examples: PDF processing, API testing, log analysis, SQL optimization

2. **What are the 3-5 core operations?**
   Be specific about what the Skill does

3. **What file types, technologies, or tools are involved?**
   List explicitly: PDF files, REST APIs, SQL databases, etc.

4. **Who will use this and how often?**
   Helps determine scope and examples needed

5. **Should it be project-level or user-level?**
   - Project: `.claude/skills/` (shared with team, in git)
   - User: `~/.claude/skills/` (personal, all projects)

**Output of Step 1:** Clear understanding of Skill purpose and scope

---

### Step 2: Description Crafting ⭐ MOST CRITICAL (10-15 min)

**This determines if the Skill will activate. Spend time here.**

#### Description Formula

Use this proven structure:

```
[ACTION VERBS] [CAPABILITIES] using [TECHNOLOGIES/TOOLS].
[ADDITIONAL CAPABILITIES].
Use when [TRIGGER SCENARIOS].
```

**Components breakdown:**

**1. Action verbs (first sentence):**
- Extract, analyze, test, validate, generate, debug, optimize, convert, process, parse
- Include 5+ action verbs

**2. Capabilities (what it does):**
- Specific operations, not vague descriptions
- Mention different modes or approaches

**3. Technologies/tools:**
- File types (.pdf, .json, .log)
- Frameworks (REST, OpenAPI, SQL)
- Libraries (pandas, eslint, pytest)

**4. Trigger scenarios:**
- "Use when working with [X]"
- "Use for [Y] tasks"
- "Use when [Z] situations"

#### Example Descriptions

**PDF Processor:**
```yaml
description: Extract text, images, and tables from PDFs. Fill PDF forms, merge documents, convert between formats (PDF↔Word). Use when working with PDF files or document processing tasks.
```

**Analysis:**
- ✅ Action verbs: Extract, fill, merge, convert
- ✅ Capabilities: Text/image/table extraction, forms, merging, conversion
- ✅ Technologies: PDF files, Word documents
- ✅ Triggers: "PDF files", "document processing"

**API Tester:**
```yaml
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes, headers, and payloads. Compare against OpenAPI specifications, test authentication, handle rate limits. Use for API development, integration testing, or debugging HTTP services.
```

**Analysis:**
- ✅ Action verbs: Test, send, validate, check, compare
- ✅ Capabilities: Requests, validation, OpenAPI comparison, auth testing
- ✅ Technologies: REST APIs, HTTP, OpenAPI
- ✅ Triggers: "API development", "integration testing", "debugging HTTP"

**SQL Optimizer:**
```yaml
description: Analyze and optimize SQL queries for PostgreSQL and MySQL. Explain execution plans, suggest indexes, identify N+1 problems, and recommend query rewrites. Use when working with database performance issues or query optimization.
```

#### Description Validation Checklist

Before proceeding, verify:
- [ ] Includes 5+ action verbs
- [ ] Lists specific capabilities (not vague)
- [ ] Mentions 3+ technologies/file types
- [ ] Includes "Use when/for" trigger scenarios
- [ ] Contains 10+ trigger keywords users will mention
- [ ] Reads naturally (not keyword-stuffed)
- [ ] 50-200 words total
- [ ] Under 1024 characters (hard limit)
- [ ] No vague terms ("helps with", "various", "multiple")
- [ ] Passes "would I tell a colleague this?" test

**If score < 8/10:** Revise description before continuing

**Common mistakes to avoid:**
- ❌ Too vague: "Helps with documents"
- ❌ Too narrow: "Extracts tables from PDFs using tabula"
- ❌ Too broad: "Processes files and data"
- ❌ Missing tech: "Test web services" (needs REST, HTTP, API)
- ❌ No triggers: No "Use when..." clause

---

### Step 3: Directory Setup (2 min)

**Create Skill directory:**

**For project Skills:**
```bash
mkdir -p .claude/skills/skill-name
```

**For user Skills:**
```bash
mkdir -p ~/.claude/skills/skill-name
```

**Naming rules:**
- Lowercase only
- Hyphens for separation (no underscores)
- Descriptive and concise
- Max 64 characters
- Examples: `pdf-processor`, `api-tester`, `log-analyzer`

---

### Step 4: YAML Frontmatter Generation (3-5 min)

**Basic template:**
```yaml
---
name: skill-name
description: [Your crafted description from Step 2]
---
```

**With tool restrictions (optional but recommended):**

**Read-only analysis:**
```yaml
---
name: log-analyzer
description: Analyze application logs for errors, warnings, patterns, and anomalies...
allowed-tools: Read, Grep, Bash
---
```

**Full capabilities:**
```yaml
---
name: api-tester
description: Test REST APIs by sending requests, validating responses...
allowed-tools: Bash, Read, Write, WebFetch
---
```

**Available tools:**
- Read, Write, Edit - File operations
- Bash - Command execution
- Grep, Glob - Search operations
- Task - Delegate to subagents
- WebFetch, WebSearch - Web operations
- MCP tools (if available)

**Validation checklist:**
- [ ] Opening `---` on line 1
- [ ] Closing `---` after fields
- [ ] `name` matches directory name exactly
- [ ] `description` ≤1024 characters
- [ ] If `allowed-tools`, uses comma-separated format (not array)
- [ ] No tab characters (spaces only)
- [ ] No smart quotes (straight quotes only)

---

### Step 5: Content Development (15-45 min)

**Structure your SKILL.md:**

```markdown
---
[YAML frontmatter]
---

# [Skill Name]: [Brief Tagline]

[1-2 paragraph overview of what this Skill does and why it's useful]

## Core Capabilities

[Bulleted list of main capabilities]

## Methodology / Workflow

[Step-by-step process for using this Skill]

### Step 1: [First Step]
[Detailed instructions]

### Step 2: [Second Step]
[Detailed instructions]

[Continue for all major steps...]

## Examples

[3-5 concrete examples showing real usage]

### Example 1: [Scenario Name]
[Complete example with inputs and outputs]

[More examples...]

## Common Patterns

[Frequently used patterns or shortcuts]

## Troubleshooting

[Common issues and solutions]
```

**Content requirements:**
- **Minimum 3 examples** (recommend 5)
- **Concrete, not generic** - Real scenarios
- **Step-by-step methodology** - Actionable instructions
- **Code blocks** where applicable
- **No placeholders** - No "TODO" or "[FILL IN]"

**Supporting files (optional):**
Create additional files if Skill is complex:
- `examples.md` - Extended examples
- `reference.md` - Detailed reference docs
- `templates/` - Reusable templates or scripts

---

### Step 6: Validation (5-10 min)

**YAML validation:**
```bash
python3 -c "import yaml; yaml.safe_load(open('.claude/skills/skill-name/SKILL.md').read().split('---')[1])" && echo "✅ Valid YAML" || echo "❌ Invalid YAML"
```

**Expected:** No output = valid YAML

**Name/directory consistency:**
```bash
# Verify match
DIRNAME=$(basename $(pwd))
YAMLNAME=$(grep "^name:" SKILL.md | cut -d: -f2 | xargs)
if [ "$DIRNAME" == "$YAMLNAME" ]; then
  echo "✅ Name matches: $DIRNAME"
else
  echo "❌ Mismatch: directory=$DIRNAME, YAML=$YAMLNAME"
fi
```

**Description quality check:**
Run through checklist from Step 2 again.

---

### Step 7: Activation Testing ⭐ CRITICAL (10-15 min)

**Most important testing phase** - Verifies Skill will actually activate.

#### Test Protocol

**Test 1: Direct Keyword Activation**
```
User request: "[Sentence using 3+ keywords from description]"
Expected: Skill activates
```

**Example for PDF processor:**
```
User: "Can you extract tables from this quarterly-report.pdf?"
Expected: ✅ pdf-processor activates
```

**Test 2: Contextual Activation**
```
User request: "[Natural request without explicit keywords]"
Expected: Skill activates
```

**Example:**
```
User: "What data is in this invoice?" [attaches PDF]
Expected: ✅ pdf-processor activates
```

**Test 3: File-Based Activation**
```
User: "[Request with relevant file attached]"
Expected: Skill activates
```

**Test 4: Boundary Test (Negative)**
```
User: "[Request from different domain]"
Expected: Skill does NOT activate
```

**Example:**
```
User: "Can you test my REST API?"
Expected: ✅ pdf-processor does NOT activate
```

**Test 5: Synonym Test**
```
User: "[Same request with synonyms/paraphrasing]"
Expected: Skill activates
```

#### If Activation Tests Fail

**Diagnosis:**
1. Check if description contains keywords user actually used
2. Verify YAML is valid (syntax errors prevent loading)
3. Ensure session restarted (Skills load at session start)

**Fixes:**
1. **Add missing keywords** to description
2. **Test with explicit mentions** of technology names
3. **Iterate description** - Try 2-3 variations
4. **Restart Claude** - Reload Skills

**Typical iterations:** 2-4 rounds until activation is reliable

**Success criteria:**
- ✅ Activates with direct keyword mentions (Test 1)
- ✅ Activates with contextual requests (Test 2)
- ✅ Activates with relevant file types (Test 3)
- ✅ Does NOT activate for unrelated requests (Test 4)

---

### Step 8: Documentation (3 min)

**Add to project README:**

```markdown
## Claude Skills

### [Skill Name]
**Purpose:** [Brief purpose]
**Activation:** [Trigger examples]
**File:** `.claude/skills/skill-name/SKILL.md`

**Example usage:**
- "[Example 1]"
- "[Example 2]"
- "[Example 3]"
```

**Commit to git (project Skills):**
```bash
git add .claude/skills/skill-name/
git commit -m "Add [skill-name] Skill for [purpose]"
git push
```

---

## Complete Skill Examples

### Example 1: Log Analyzer Skill

```yaml
---
name: log-analyzer
description: Analyze application logs for errors, warnings, patterns, and anomalies. Parse log formats (JSON, plain text, syslog), identify error trends, correlate events, extract stack traces. Use when debugging issues, investigating problems, or analyzing log files.
allowed-tools: Read, Grep, Bash
---

# Log Analyzer: Debug Issues Through Log Analysis

Comprehensive log analysis to identify errors, patterns, and root causes in application logs.

## Core Capabilities

- Parse multiple log formats (JSON, plain text, syslog, custom)
- Identify error patterns and trends
- Extract and analyze stack traces
- Correlate related log events
- Detect anomalies and unusual patterns
- Generate timeline of critical events
- Recommend remediation actions

## Methodology

### Step 1: Understand Log Format

First, examine the log structure:

```bash
head -20 application.log
```

Identify:
- Timestamp format
- Log level (ERROR, WARN, INFO, DEBUG)
- Component/service name
- Message structure
- JSON vs plain text

### Step 2: Scan for Errors

Search for error indicators:

```bash
# Find errors
grep -i "error\|exception\|fail\|fatal" application.log

# Count by error type
grep -i "error" application.log | sort | uniq -c | sort -rn
```

### Step 3: Extract Stack Traces

Identify and extract complete stack traces:

```bash
# Find stack traces (multi-line)
grep -A 20 "Exception:" application.log
```

### Step 4: Identify Patterns

Look for recurring issues:

- Frequency of specific errors
- Time-based patterns (spikes at certain times)
- Correlated errors (errors that occur together)
- Cascading failures

### Step 5: Timeline Analysis

Create timeline of critical events:

```bash
# Extract timestamps and errors
grep "ERROR" application.log | cut -d' ' -f1-2
```

Build chronological sequence to understand causation.

### Step 6: Root Cause Hypothesis

Based on patterns:
- What triggered the initial error?
- What cascaded from it?
- Is it related to external factors (load, network, etc.)?
- Are there configuration issues?

## Examples

### Example 1: Database Connection Errors

**Scenario:** Application experiencing intermittent database failures

**Analysis:**
```bash
grep -i "database\|connection" application.log | grep -i "error"
```

**Findings:**
- 127 connection timeout errors
- All between 2:00 AM - 2:15 AM
- Coincides with backup window

**Root Cause:** Database backup process causing connection pool exhaustion

**Recommendation:** Increase connection pool size or adjust backup schedule

### Example 2: Memory Leak Detection

**Scenario:** Application getting slower over time

**Analysis:**
```bash
grep "OutOfMemoryError" application.log
grep -B 10 "OutOfMemoryError" application.log
```

**Findings:**
- Memory errors after 6-8 hours uptime
- Heap dump shows large object retention
- Correlates with specific API endpoint usage

**Root Cause:** Memory leak in image processing module

**Recommendation:** Review image processing code for unreleased resources

### Example 3: API Failure Correlation

**Scenario:** Random API failures

**Analysis:**
```bash
# Extract API errors with timestamps
grep "API.*failed" application.log | awk '{print $1, $2, $NF}'
```

**Findings:**
- API failures only for external service
- All return 503 status code
- External service had documented outage

**Root Cause:** Third-party service degradation

**Recommendation:** Implement circuit breaker pattern

## Common Patterns

### Pattern 1: Rate Limiting Detection

```bash
grep -i "rate limit\|429\|too many requests" application.log
```

### Pattern 2: Authentication Issues

```bash
grep -i "401\|403\|unauthorized\|forbidden" application.log
```

### Pattern 3: Resource Exhaustion

```bash
grep -i "timeout\|too many\|exhausted\|limit exceeded" application.log
```

## Troubleshooting

### Issue: Log File Too Large

**Solution:** Sample or filter before analysis
```bash
tail -10000 application.log | grep "ERROR"
```

### Issue: Unclear Error Messages

**Solution:** Expand context around errors
```bash
grep -B 5 -A 5 "ERROR" application.log
```

### Issue: Multiple Log Sources

**Solution:** Merge and sort by timestamp
```bash
sort -m service1.log service2.log | grep "ERROR"
```
```

---

### Example 2: API Tester Skill

```yaml
---
name: api-tester
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes, headers, and payloads. Compare against OpenAPI specifications, test authentication, handle rate limits. Use for API development, integration testing, or debugging HTTP services.
allowed-tools: Bash, Read, Write, WebFetch
---

# API Tester: Comprehensive REST API Testing

Test REST APIs thoroughly with request validation, response checking, and OpenAPI spec comparison.

## Core Capabilities

- Send HTTP requests (GET, POST, PUT, DELETE, PATCH)
- Validate response status codes and headers
- Check response payload structure and content
- Compare against OpenAPI/Swagger specifications
- Test authentication mechanisms (Bearer, Basic, API Key)
- Handle rate limiting and retries
- Test error cases and edge conditions

## Methodology

### Step 1: Understand the API

- Read OpenAPI/Swagger spec if available
- Identify base URL, authentication method
- List all endpoints to test
- Note rate limits and constraints

### Step 2: Prepare Test Cases

For each endpoint, test:
- ✅ Happy path (valid request → 200 OK)
- ✅ Validation errors (invalid input → 400 Bad Request)
- ✅ Authentication (missing auth → 401 Unauthorized)
- ✅ Authorization (insufficient permissions → 403 Forbidden)
- ✅ Not found (invalid resource → 404 Not Found)
- ✅ Rate limits (too many requests → 429 Too Many Requests)

### Step 3: Send Requests

Use curl with structured commands:

```bash
# GET request with headers
curl -X GET "https://api.example.com/users/123" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept: application/json" \
  -w "\nHTTP Status: %{http_code}\n"

# POST request with JSON payload
curl -X POST "https://api.example.com/users" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"John","email":"john@example.com"}' \
  -w "\nHTTP Status: %{http_code}\n"
```

### Step 4: Validate Responses

Check:
- Status code matches expected
- Response headers (Content-Type, Cache-Control, etc.)
- Response body structure (JSON schema)
- Response time (performance)

### Step 5: Compare Against Specification

If OpenAPI spec exists:
- Validate request conforms to schema
- Validate response matches defined schema
- Check required fields are present

## Examples

### Example 1: Testing User CRUD API

**GET /users/{id}:**
```bash
# Success case
curl -X GET "https://api.example.com/users/123" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept: application/json"

# Expected: 200 OK with user object

# Not found case
curl -X GET "https://api.example.com/users/99999" \
  -H "Authorization: Bearer $TOKEN"

# Expected: 404 Not Found
```

### Example 2: Testing Authentication

**No token:**
```bash
curl -X GET "https://api.example.com/users/123"
# Expected: 401 Unauthorized
```

**Invalid token:**
```bash
curl -X GET "https://api.example.com/users/123" \
  -H "Authorization: Bearer invalid_token"
# Expected: 401 Unauthorized
```

**Valid token:**
```bash
curl -X GET "https://api.example.com/users/123" \
  -H "Authorization: Bearer $VALID_TOKEN"
# Expected: 200 OK
```

### Example 3: Testing Input Validation

**POST with invalid email:**
```bash
curl -X POST "https://api.example.com/users" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"John","email":"not-an-email"}'

# Expected: 400 Bad Request with validation error
```

## Common Patterns

### Pattern 1: Batch Testing Multiple Endpoints

```bash
for endpoint in /users /posts /comments; do
  echo "Testing $endpoint"
  curl -X GET "https://api.example.com$endpoint" \
    -H "Authorization: Bearer $TOKEN" \
    -w "\nStatus: %{http_code}\n"
done
```

### Pattern 2: Rate Limit Testing

```bash
# Send rapid requests to trigger rate limit
for i in {1..100}; do
  curl -X GET "https://api.example.com/users" \
    -H "Authorization: Bearer $TOKEN" \
    -w "Request $i: %{http_code}\n"
  sleep 0.1
done
```

## Troubleshooting

### Issue: Connection Refused

**Cause:** API not running or incorrect URL

**Solution:** Verify base URL and port

### Issue: SSL Certificate Error

**Cause:** Self-signed or expired certificate

**Solution:** Use `-k` flag (development only)

### Issue: Timeout

**Cause:** API slow or unresponsive

**Solution:** Increase timeout with `--max-time` flag
```

---

## Quick Reference

### Description Formula
```
[ACTION VERBS] [CAPABILITIES] using [TECHNOLOGIES/TOOLS].
[ADDITIONAL CAPABILITIES].
Use when [TRIGGER SCENARIOS].
```

### YAML Template
```yaml
---
name: skill-name
description: [Crafted description with 10+ trigger keywords]
allowed-tools: [Comma-separated list if restricted]
---
```

### Tool Combinations
```yaml
# Read-only analysis
allowed-tools: Read, Grep, Glob

# Shell execution
allowed-tools: Bash, Read

# File manipulation
allowed-tools: Read, Write, Edit, Bash

# Web research
allowed-tools: WebSearch, WebFetch, Read
```

---

## Best Practices

✅ **DO:**
- Spend time crafting description (Step 2)
- Include 10+ trigger keywords
- Test activation thoroughly (Step 7)
- Provide 3-5 concrete examples
- Use appropriate tool restrictions
- Document in README (project Skills)
- Iterate on description until activation reliable

❌ **DON'T:**
- Rush through description writing
- Use vague language ("helps with", "various")
- Skip activation testing
- Make description too broad or too narrow
- Give excessive tool permissions
- Forget to restart Claude after creating Skill
- Skip documentation

---

## Success Criteria

A well-built Skill has:
- ✅ Specific description with 10+ trigger keywords
- ✅ Valid YAML frontmatter
- ✅ Clear step-by-step methodology
- ✅ 3-5 concrete examples
- ✅ Appropriate tool access
- ✅ 4/5 activation tests pass
- ✅ No placeholders or TODOs
- ✅ Documented in README (if project Skill)

**Target quality:** Grade A (≥0.90 on validation framework)

---

## Troubleshooting Common Issues

### Issue 1: Skill Never Activates

**Symptoms:** User mentions relevant keywords but Skill doesn't activate

**Common causes:**
1. Description too vague (lacks specific keywords)
2. YAML syntax error (Skill doesn't load)
3. Name mismatch (directory vs YAML)
4. Session not restarted

**Fixes:**
1. **Revise description** - Add 10+ specific trigger keywords
2. **Validate YAML** - Fix syntax errors
3. **Match names** - Ensure consistency
4. **Restart session** - Reload Skills

**Example fix:**
```yaml
# Before (too vague)
description: Helps with APIs

# After (specific)
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes. Use for API testing, debugging HTTP services, or validating API endpoints.
```

### Issue 2: Skill Activates Too Broadly

**Symptoms:** Skill activates for unrelated requests

**Cause:** Description too broad, generic keywords

**Fix:**
1. **Narrow description** to specific use cases
2. **Remove generic terms** ("data", "files", "processing")
3. **Add domain-specific vocabulary**
4. **Test boundary cases**

### Issue 3: YAML Parsing Errors

**Common errors:**
```yaml
# ❌ Missing closing ---
---
name: my-skill
description: Something

# ❌ Array syntax for allowed-tools
allowed-tools:
  - Read
  - Write

# ❌ Tab character
---
name: my-skill
	description: Something
```

**Fixes:**
- Always close with `---`
- Use comma-separated: `allowed-tools: Read, Write`
- Use spaces, never tabs

---

## Next Steps

After creating the skill:
1. Test activation with various phrasings
2. Get feedback from users (project Skills)
3. Monitor actual usage patterns
4. Refine description based on false positives/negatives
5. Consider creating related Skills for complementary tasks

**Need validation?** Use the `artifact-validator` Skill to check Skill quality.

**Unsure if Skill is right?** Use the `artifact-advisor` Skill to confirm artifact type choice.

**Want more Skills?** Use `/suggest-artifacts` to identify additional automation opportunities.

---

**Remember: The description (Step 2) and activation testing (Step 7) are the most critical steps. These determine if your Skill will actually be useful!**
