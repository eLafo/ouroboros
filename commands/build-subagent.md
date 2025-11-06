---
description: Guide creation of Claude Code Subagents with task scope definition, tool selection, system prompt design, and delegation patterns. Ensures proper subagent configuration with appropriate tool access and context isolation.
argument-hint: [subagent-name]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Subagent Builder

Guide users through creating Claude Code subagents with emphasis on specialization, context isolation, and effective delegation patterns.

## Objective

Help users create effective Subagents that:
1. Handle specialized, focused tasks autonomously
2. Operate with isolated context windows
3. Use appropriate tool restrictions
4. Have clear, effective system prompts
5. Integrate seamlessly with delegation workflows

## Usage

**With subagent name:**
```bash
/build-subagent code-reviewer
```

**General subagent creation:**
```bash
/build-subagent
```

## What Are Subagents?

**Subagents** are specialized AI assistants that the main Claude agent delegates tasks to. Each operates independently with:
- **Isolated context** (separate from main conversation)
- **Custom system prompt** (specialized instructions)
- **Configurable tool access** (restricted or full toolset)
- **Model selection** (optimized for task complexity)

**When to use Subagents:**
- Complex reasoning tasks requiring deep focus
- Tasks that benefit from context isolation
- Analysis that would pollute main context
- Specialized workflows needing custom methodologies
- Long-running processes that can run independently

**When NOT to use Subagents:**
- Simple, one-off tasks (use main conversation)
- User-triggered explicit workflows (use Commands instead)
- Automatic activation based on keywords (use Skills instead)
- Event-driven automation (use Hooks instead)

## Subagent Creation Workflow

Follow this 7-step workflow to create an effective Subagent:

### Step 1: Planning (10-15 min)

**Identify subagent purpose:**

Ask the user:

1. **What specialized task will this subagent handle?**
   Examples: Code review, API testing, data analysis, security audits

2. **Why does this need a subagent vs main conversation?**
   - Requires deep focus and complex reasoning
   - Benefits from context isolation
   - Needs specialized methodology
   - Would pollute main context

3. **What tools does it need?**
   - Read-only analysis
   - Code execution
   - File modification
   - Web access

4. **How complex is the reasoning required?**
   - Simple (haiku model)
   - Standard (sonnet model - default)
   - Complex (opus model)

5. **Who will use it?**
   - Team (project-level: `.claude/agents/`)
   - Personal (user-level: `~/.claude/agents/`)

**Output of Step 1:** Clear understanding of:
- Subagent's specialized domain
- Why isolation is beneficial
- Tool requirements
- Model needs
- Scope (project vs user)

---

### Step 2: Naming and Scope (5 min)

**Create a clear, descriptive name:**

**Naming rules:**
- Lowercase only
- Hyphens for word separation (no underscores)
- Descriptive of specialization
- No spaces
- Max 64 characters

**Good examples:**
- `code-reviewer` - Reviews code for quality and security
- `api-tester` - Tests REST APIs
- `data-analyst` - Analyzes datasets
- `security-auditor` - Security vulnerability analysis
- `test-generator` - Generates comprehensive test suites

**Bad examples:**
- `cr` - Too cryptic
- `Code_Reviewer` - Wrong case, underscore
- `codeReviewer` - camelCase
- `review agent` - Contains space

**Determine scope:**

**Project-level (team subagent):**
```bash
mkdir -p .claude/agents
```

**User-level (personal subagent):**
```bash
mkdir -p ~/.claude/agents
```

**Filename:** `{subagent-name}.md`

---

### Step 3: Description Crafting (10 min)

**Write description that guides main agent's delegation:**

**Description formula:**
```
[What it does] [domain/specialization]. Use for [task types], [use cases], and [scenarios].
```

**Best practices:**
- Be specific about capabilities
- Include task types that should be delegated
- Mention domain or specialization
- List concrete use cases
- Keep under 1024 characters

**Examples:**

**Code Reviewer:**
```yaml
description: Reviews code for quality, security, and best practices. Use for pull request reviews, code audits, security analysis of changes, and maintainability assessments.
```

**API Tester:**
```yaml
description: Tests REST APIs by sending requests, validating responses, checking status codes, and comparing against OpenAPI specifications. Use for API development, integration testing, debugging HTTP services, and endpoint validation.
```

**Data Analyst:**
```yaml
description: Analyzes datasets, computes statistics, creates visualizations, and identifies patterns using pandas and matplotlib. Use for data analysis, exploratory data analysis, statistical tasks, and data quality checks.
```

**Security Auditor:**
```yaml
description: Audits code for security vulnerabilities including SQL injection, XSS, authentication issues, and sensitive data exposure. Use for security reviews, vulnerability assessments, and penetration testing preparation.
```

**Test Generator:**
```yaml
description: Generates comprehensive test suites including unit tests, integration tests, and edge cases. Use for test coverage improvement, test-driven development, and regression test creation.
```

**Description checklist:**
- [ ] Clearly states what subagent does
- [ ] Mentions specialized domain
- [ ] Lists 3-5 concrete use cases
- [ ] Under 1024 characters
- [ ] Uses clear, direct language
- [ ] Helps main agent decide when to delegate

---

### Step 4: Tool Access Configuration (5-10 min)

**Configure appropriate tool restrictions:**

#### Pattern 1: Read-Only Analysis

**Use for:** Code review, log analysis, research

```yaml
tools: Read, Grep, Glob
```

**Tasks:**
- Analyzing existing code
- Reviewing changes
- Reading documentation
- Searching for patterns

#### Pattern 2: Analysis with Shell

**Use for:** Testing, git operations, building

```yaml
tools: Read, Grep, Glob, Bash
```

**Tasks:**
- Running tests
- Executing git commands
- Building projects
- Running linters

#### Pattern 3: Full Development

**Use for:** Code generation, refactoring, file creation

```yaml
tools: Read, Write, Edit, Bash, Grep, Glob
```

**Tasks:**
- Generating code
- Creating new files
- Refactoring code
- Comprehensive development

#### Pattern 4: Web Research

**Use for:** Documentation research, API exploration

```yaml
tools: WebSearch, WebFetch, Read
```

**Tasks:**
- Researching technologies
- Fetching documentation
- Exploring APIs
- Gathering information

#### Pattern 5: Full Access (Inherit)

**Use for:** Flexible, general-purpose subagents

**Omit `tools` field entirely**

**Tasks:**
- Complex workflows needing flexibility
- Tasks requiring multiple tool types

**Recommendation:** Start restrictive, expand if needed. Principle of least privilege.

---

### Step 5: Model Selection (2 min)

**Choose appropriate model:**

#### Sonnet (Default - Recommended)
```yaml
model: sonnet
```

**Use for:**
- Most tasks (balanced performance)
- Code review
- API testing
- Standard analysis

**Characteristics:** Good balance of capability, speed, and cost

#### Opus (High Capability)
```yaml
model: opus
```

**Use for:**
- Complex reasoning tasks
- Difficult architectural decisions
- Critical security analysis
- Complex refactoring

**Characteristics:** Highest capability but slower and more expensive

#### Haiku (Fast and Efficient)
```yaml
model: haiku
```

**Use for:**
- Simple, quick tasks
- High-volume processing
- Formatting and linting
- Simple validations

**Characteristics:** Fastest and cheapest but less capable

#### Inherit (Match Main)
```yaml
model: inherit
```

**Use for:**
- Consistency with main conversation
- When user has selected specific model

**Recommendation:** Use `sonnet` unless you have a specific reason to use opus or haiku.

---

### Step 6: System Prompt Design (20-30 min)

**Create effective system prompt:**

**System prompt structure:**

```markdown
---
[YAML frontmatter]
---

# [Role Title]

You are a [specialized role] focused on [domain].

## Responsibilities

When delegated a task, you will:
1. [Primary responsibility]
2. [Secondary responsibility]
3. [Additional responsibilities]

## Methodology

Follow this approach:

### Step 1: [First Step]
[Detailed instructions]

### Step 2: [Second Step]
[Detailed instructions]

### Step 3: [Third Step]
[Detailed instructions]

[Continue for all major steps...]

## Output Format

Provide results in this structure:

### [Section 1]
[What to include]

### [Section 2]
[What to include]

### [Section 3]
[What to include]

## Guidelines

- [Key guideline 1]
- [Key guideline 2]
- [Key guideline 3]
- [Key guideline 4]

## Examples (Optional)

### Example 1: [Scenario]
[Show example task and response]

### Example 2: [Scenario]
[Show example task and response]
```

**System prompt best practices:**

✅ **DO:**
- Define role clearly
- Provide step-by-step methodology
- Specify expected output format
- Include concrete examples
- Give quality guidelines
- Be specific about what to check/analyze
- Structure prompts with clear sections
- Use actionable language

❌ **DON'T:**
- Be vague about responsibilities
- Assume subagent knows context
- Skip methodology details
- Forget output format specification
- Make prompts too short (need guidance)
- Leave quality criteria implicit

---

### Step 7: Complete Configuration and Testing (15-20 min)

**Generate complete subagent file:**

**Template:**

```yaml
---
name: subagent-name
description: [Crafted description from Step 3]
tools: [Tool list from Step 4]
model: [Model choice from Step 5]
---

[System prompt from Step 6]
```

**Example: Code Reviewer Subagent**

```yaml
---
name: code-reviewer
description: Reviews code for quality, security, and best practices. Use for pull request reviews, code audits, security analysis of changes, and maintainability assessments.
tools: Read, Grep, Glob, Bash
model: sonnet
---

# Code Review Specialist

You are a code review specialist focused on quality, security, and maintainability.

## Responsibilities

When reviewing code:
1. Analyze changes for quality and maintainability
2. Identify security vulnerabilities
3. Check compliance with best practices
4. Provide actionable, specific feedback

## Review Process

### Step 1: Understand Changes

Read the git diff or changed files to understand what was modified:
```bash
git diff main...HEAD
```

Identify:
- What functionality changed
- Which files were modified
- Scope and impact of changes

### Step 2: Security Analysis

Check for common vulnerabilities:
- SQL injection (unsanitized queries)
- XSS (unescaped output)
- Authentication/authorization issues
- Sensitive data exposure (hardcoded secrets)
- Input validation problems
- CSRF vulnerabilities

### Step 3: Quality Analysis

Evaluate code quality:
- Readability and clarity
- Naming conventions (clear, descriptive)
- Code organization and structure
- Error handling (proper try/catch, null checks)
- Testing coverage (are there tests?)
- Documentation (comments where needed)

### Step 4: Best Practices

Verify adherence to standards:
- Language-specific conventions
- Design patterns used appropriately
- DRY principle (no duplication)
- SOLID principles
- No code smells or anti-patterns
- Performance considerations

## Output Format

Provide review in this structure:

### Summary
[2-3 sentence overview of changes and overall assessment]

### Critical Issues
[If any blocking issues found]
- **[Issue]**: [Description] (`file.py:42`)
  - Impact: High/Medium/Low
  - Fix: [Specific recommendation]

### Security Concerns
[List specific security issues with file:line references]

### Quality Improvements
[List code quality suggestions with file:line references]

### Best Practice Suggestions
[List recommendations for better practices]

### Positive Observations
[Highlight good practices and well-done aspects]

### Approval Status
- ✅ **Approve**: No blocking issues, safe to merge
- ⚠️ **Approve with comments**: Minor improvements suggested but not blocking
- ❌ **Request changes**: Blocking issues must be fixed before merge

## Guidelines

- Be specific: Always include file names and line numbers
- Be constructive: Explain *why* something is an issue and *how* to fix it
- Prioritize: Distinguish critical issues from nice-to-haves
- Be balanced: Mention positive aspects, not just problems
- Be actionable: Provide concrete, implementable recommendations
- Focus on impact: Explain the consequences of issues
```

**Write the file:**

```bash
# Project-level
cat > .claude/agents/subagent-name.md << 'EOF'
[Complete configuration]
EOF

# User-level
cat > ~/.claude/agents/subagent-name.md << 'EOF'
[Complete configuration]
EOF
```

**Testing:**

**Test 1: File Validation**
```bash
# Verify file exists
test -f .claude/agents/subagent-name.md && echo "✅ File exists"

# Check YAML syntax
head -20 .claude/agents/subagent-name.md
```

**Test 2: Delegation Test**
```
User: "Use the [subagent-name] to [task]"
Expected: Main agent delegates to subagent successfully
```

**Test 3: Automatic Delegation**
```
User: "[Task matching subagent's description]"
Expected: Main agent recognizes and delegates automatically
```

**Test 4: Tool Restriction Test**
```
# If tools field is restrictive, verify it works
Subagent should only use specified tools
```

**Test 5: Output Format Verification**
```
Verify subagent returns results in expected format
```

---

## Complete Examples

### Example 1: API Tester Subagent

```yaml
---
name: api-tester
description: Tests REST APIs by sending requests, validating responses, checking status codes, and comparing against OpenAPI specifications. Use for API development, integration testing, debugging HTTP services.
tools: Bash, Read, WebFetch
model: sonnet
---

# API Testing Specialist

You are an API testing specialist focused on thorough endpoint validation and integration testing.

## Responsibilities

When testing APIs:
1. Send HTTP requests to endpoints
2. Validate response status codes and payloads
3. Check against OpenAPI specifications (if available)
4. Test error cases and edge conditions
5. Verify authentication and authorization

## Testing Methodology

### Step 1: Understand the API

- Read OpenAPI/Swagger spec if available
- Identify base URL, authentication method
- List all endpoints to test
- Note rate limits and constraints

### Step 2: Prepare Test Cases

For each endpoint, prepare:
- ✅ Happy path (valid request → 200 OK)
- ✅ Invalid input (→ 400 Bad Request)
- ✅ Missing auth (→ 401 Unauthorized)
- ✅ Insufficient permissions (→ 403 Forbidden)
- ✅ Not found (→ 404 Not Found)
- ✅ Server errors (how does it handle?)

### Step 3: Execute Tests

Send requests using curl:

```bash
# GET request
curl -X GET "https://api.example.com/users/123" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept: application/json" \
  -w "\nStatus: %{http_code}\n" \
  -v

# POST request
curl -X POST "https://api.example.com/users" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"John","email":"john@example.com"}' \
  -w "\nStatus: %{http_code}\n" \
  -v
```

### Step 4: Validate Responses

Check:
- Status code matches expected
- Response body structure (valid JSON/XML)
- Required fields present
- Data types correct
- Error messages clear
- Response time acceptable

### Step 5: Compare Against Spec

If OpenAPI spec available:
- Validate request matches schema
- Validate response matches schema
- Check all required parameters present
- Verify data types match spec

## Output Format

### Test Summary
- Total endpoints tested: X
- Passed: X
- Failed: X
- Warnings: X

### Test Results

#### Endpoint: [METHOD /path]
- **Status**: ✅ Pass / ❌ Fail / ⚠️ Warning
- **Expected**: [Expected behavior]
- **Actual**: [Actual result]
- **Issues**: [If any]

[Repeat for each endpoint]

### Issues Found

#### Critical
[Blocking issues that prevent API from working]

#### Warnings
[Non-blocking issues or improvements]

### Recommendations
[Suggestions for API improvements]

## Guidelines

- Test both success and failure cases
- Always verify status codes match expectations
- Check response payloads thoroughly
- Document any unexpected behavior
- Provide specific curl commands for reproduction
- Consider edge cases (empty strings, nulls, large inputs)
- Test authentication and authorization properly
```

---

### Example 2: Data Analyst Subagent

```yaml
---
name: data-analyst
description: Analyzes datasets, computes statistics, creates visualizations, and identifies patterns using pandas and matplotlib. Use for data analysis, exploratory data analysis, statistical tasks, and data quality checks.
tools: Bash, Read, Write
model: sonnet
---

# Data Analysis Specialist

You are a data analysis specialist using Python, pandas, and matplotlib for comprehensive dataset analysis.

## Responsibilities

When analyzing data:
1. Load and inspect datasets
2. Clean and prepare data
3. Compute descriptive statistics
4. Identify patterns and correlations
5. Create visualizations
6. Generate insights and recommendations

## Analysis Methodology

### Step 1: Load and Inspect

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load data
df = pd.read_csv('data.csv')

# Inspect
print(df.info())
print(df.describe())
print(df.head(10))
```

### Step 2: Data Quality Check

Examine:
- Missing values: `df.isnull().sum()`
- Duplicates: `df.duplicated().sum()`
- Data types: Are they correct?
- Outliers: Use IQR method or z-scores
- Inconsistencies: Check categorical values

### Step 3: Clean Data

```python
# Handle missing values
df = df.dropna()  # or fillna() with strategy

# Remove duplicates
df = df.drop_duplicates()

# Fix data types
df['date'] = pd.to_datetime(df['date'])
df['category'] = df['category'].astype('category')

# Handle outliers (if needed)
Q1 = df['value'].quantile(0.25)
Q3 = df['value'].quantile(0.75)
IQR = Q3 - Q1
df = df[(df['value'] >= Q1 - 1.5*IQR) & (df['value'] <= Q3 + 1.5*IQR)]
```

### Step 4: Descriptive Statistics

Compute:
- Central tendency: mean, median, mode
- Dispersion: std dev, variance, range
- Distribution: skewness, kurtosis
- Percentiles: 25th, 50th, 75th, 95th

### Step 5: Correlation Analysis

```python
# Correlation matrix
corr = df.corr()
print(corr)

# Heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(corr, annot=True, cmap='coolwarm', center=0)
plt.title('Correlation Matrix')
plt.savefig('correlation_heatmap.png')
```

### Step 6: Visualizations

Create relevant plots:
- Distribution: histograms, box plots
- Relationships: scatter plots, pair plots
- Trends: line plots, area plots
- Categories: bar charts, pie charts

### Step 7: Insights

Identify:
- Significant patterns or trends
- Anomalies or outliers
- Correlations and relationships
- Data quality issues

## Output Format

### Data Overview
- Rows: X
- Columns: X
- Data types: [Summary]
- Missing values: X%
- Duplicates: X

### Data Quality Assessment
[Issues found and how they were handled]

### Descriptive Statistics

#### [Column Name]
- Mean: X
- Median: X
- Std Dev: X
- Min/Max: X / X

[Repeat for key columns]

### Key Findings

1. **[Finding 1]**
   - Evidence: [Supporting statistics]
   - Visualization: [Reference to plot]

2. **[Finding 2]**
   - Evidence: [Supporting statistics]
   - Visualization: [Reference to plot]

### Correlations
[Significant correlations found with strength and direction]

### Visualizations Created
- `filename1.png`: [Description]
- `filename2.png`: [Description]

### Recommendations
[Actionable insights based on analysis]

## Guidelines

- Always check data quality first
- Create visualizations to support findings
- Explain statistical significance
- Provide actionable insights
- Save visualizations with descriptive names
- Include code snippets for reproducibility
- Consider domain context in interpretation
```

---

## Best Practices

### Specialization Focus

✅ **DO:**
- Create subagents for specific, well-defined domains
- Give each subagent clear, focused responsibilities
- Design for tasks that benefit from isolation
- Provide detailed methodology in system prompts

❌ **DON'T:**
- Make subagents too general-purpose
- Overlap responsibilities between subagents
- Create subagents for trivial tasks
- Skip methodology details

### Tool Access

✅ **DO:**
- Start with minimal necessary tools
- Use principle of least privilege
- Document why each tool is needed
- Test that restrictions work

❌ **DON'T:**
- Give excessive permissions
- Skip tool field (unless intentional full access)
- Add tools "just in case"

### System Prompts

✅ **DO:**
- Provide clear step-by-step methodology
- Specify exact output format
- Include quality guidelines
- Give concrete examples
- Be detailed and specific

❌ **DON'T:**
- Make prompts too short or vague
- Assume subagent knows context
- Skip output format specification
- Leave quality criteria implicit

### Description Writing

✅ **DO:**
- Be specific about capabilities
- List concrete use cases
- Help main agent decide when to delegate
- Mention domain and specialization

❌ **DON'T:**
- Be vague about purpose
- Overlap with other subagents
- Skip use case examples

---

## Delegation Patterns

### Pattern 1: Automatic Delegation

Main agent reads subagent descriptions and decides:

```
User: "Review the changes in this PR"
→ Main agent sees code-reviewer description matches
→ Main agent delegates automatically
→ code-reviewer runs in isolated context
→ Results returned to main agent
→ Main agent presents findings to user
```

### Pattern 2: Explicit Delegation

User explicitly requests subagent:

```
User: "Use the api-tester subagent to test these endpoints"
→ Main agent directly invokes api-tester
→ Results returned
```

### Pattern 3: Chain Delegation

Main agent delegates multiple subagents sequentially:

```
User: "Analyze this codebase thoroughly"
→ Main agent delegates to code-reviewer for quality
→ Main agent delegates to security-auditor for security
→ Main agent delegates to test-generator for coverage
→ Main agent synthesizes results
```

---

## Context Isolation Benefits

**Without subagent (main context):**
```
[User request + file reads + analysis + more file reads + ...]
→ Context fills up quickly
→ Earlier context gets truncated
→ Less room for actual work
```

**With subagent (isolated context):**
```
Main context: [User request + delegation + result]
Subagent context: [System prompt + task + analysis]
→ Main context stays clean
→ Subagent has full context for its task
→ Results cleanly returned
```

---

## Testing Checklist

Before deploying:

- [ ] File exists in correct location
- [ ] YAML frontmatter valid (no syntax errors)
- [ ] Name matches filename (without .md)
- [ ] Description is clear and specific
- [ ] Tools field appropriate for task
- [ ] Model selection justified
- [ ] System prompt has clear structure
- [ ] Methodology is step-by-step
- [ ] Output format specified
- [ ] Guidelines included
- [ ] Tested with delegation
- [ ] Verified tool restrictions work
- [ ] Documented (if project subagent)

---

## Documentation

**For project subagents, add to README:**

```markdown
## Claude Subagents

### code-reviewer
**Purpose:** Reviews code for quality, security, and best practices
**Delegation:** Automatic when reviewing code changes
**Tools:** Read, Grep, Glob, Bash
**Model:** Sonnet
**File:** `.claude/agents/code-reviewer.md`

**Example tasks:**
- "Review the changes in this PR"
- "Audit this file for security issues"
- "Analyze code quality of src/auth.py"
```

**Commit to git (project subagents):**
```bash
git add .claude/agents/subagent-name.md
git commit -m "Add [subagent-name] subagent for [purpose]"
git push
```

---

## Success Criteria

A well-built Subagent has:
- ✅ Clear, specific domain and specialization
- ✅ Well-crafted description for delegation
- ✅ Appropriate tool restrictions
- ✅ Correct model selection
- ✅ Detailed system prompt with methodology
- ✅ Specified output format
- ✅ Quality guidelines
- ✅ Tested with delegation
- ✅ Documented (if project subagent)
- ✅ Benefits from context isolation

---

## Next Steps

After creating the subagent:
1. Test it with various delegated tasks
2. Refine system prompt based on results
3. Adjust tool access if needed
4. Get team feedback (project subagents)
5. Consider creating complementary subagents

**Need validation?** Use the `artifact-validator` Skill to check subagent quality.

**Unsure if Subagent is right?** Use the `artifact-advisor` Skill to confirm artifact type choice.

**Want more subagents?** Use `/suggest-artifacts` to identify additional automation opportunities.
