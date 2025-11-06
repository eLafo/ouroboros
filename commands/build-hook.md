---
description: Guide Hook creation with mandatory security review checklist, event selection, and safety validation. Hooks execute arbitrary commands automatically and require careful security consideration.
argument-hint: [hook-name]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Hook Builder

⚠️ **SECURITY WARNING**: Hooks execute arbitrary shell commands automatically in response to events. You are solely responsible for command safety and consequences, including potential data loss or system damage.

**This guide emphasizes security review as a mandatory requirement for all Hooks.**

## Objective

Help users create safe, effective Hooks that:
1. ⚠️ **PASS MANDATORY SECURITY REVIEW**
2. Respond to appropriate events
3. Use proper variable quoting and validation
4. Handle errors gracefully
5. Follow principle of least privilege
6. Are tested thoroughly before deployment

## Critical Security Requirements

**🛑 BEFORE YOU BEGIN:**

Every Hook MUST pass security review. No exceptions.

**Security Review Checklist** (Complete for EVERY Hook):
- [ ] No arbitrary code execution vulnerabilities
- [ ] All variables properly quoted: `"$VAR"` not `$VAR`
- [ ] Input validation present for external data
- [ ] Error handling exists (set -e, trap, etc.)
- [ ] No hardcoded credentials or secrets
- [ ] Timeout set appropriately
- [ ] Destructive operations have confirmations
- [ ] Failure modes are safe (fail-closed)
- [ ] Logging doesn't expose sensitive data
- [ ] Principle of least privilege applied
- [ ] Tested in isolated environment first
- [ ] Code reviewed by security-conscious peer

**If ANY checkbox is unchecked, DO NOT deploy the Hook.**

---

## Usage

**With hook name:**
```bash
/build-hook pre-commit-validator
```

**General hook creation:**
```bash
/build-hook
```

---

## What Are Hooks?

**Hooks** are event-driven scripts that execute automatically in response to Claude Code events.

**Characteristics:**
- **Event-driven**: Triggered by specific events (tool use, prompt submit, etc.)
- **Automated**: Execute without user intervention
- **Powerful**: Can block operations or integrate with external systems
- **HIGH-RISK**: Execute arbitrary commands with potential for damage

**When to use Hooks:**
- Validation or policy enforcement (linting, tests)
- Integration with external systems (CI/CD, logging)
- Automatic safeguards (prevent dangerous operations)
- Workflow automation (cleanup, notifications)

**When NOT to use Hooks:**
- One-time tasks (use Commands instead)
- User-triggered workflows (use Commands)
- Tasks requiring reasoning (use Skills or Subagents)
- Anything that doesn't need to be automatic

---

## Hook Creation Workflow

Follow this 9-step workflow with mandatory security review:

### Step 1: Planning and Risk Assessment (10-15 min)

**Identify hook purpose:**

Ask the user:

1. **What event should trigger this hook?**
   - PreToolUse (before tool execution - can block)
   - PostToolUse (after successful tool execution)
   - UserPromptSubmit (when user submits prompt)
   - SessionStart (session initialization)
   - SessionEnd (session termination)

2. **What will the hook do?**
   Examples: Run linter, execute tests, validate input, log actions

3. **What's the risk level?**
   - **HIGH**: Destructive operations (delete, deploy, modify prod)
   - **MEDIUM**: File modifications, external integrations
   - **LOW**: Read-only operations (logging, analysis)

4. **What could go wrong?**
   - Command injection?
   - Data loss?
   - Unauthorized access?
   - Denial of service?
   - Information disclosure?

5. **Where should it be configured?**
   - Project-level shared: `.claude/settings.json` (committed)
   - Project-level personal: `.claude/settings.local.json` (gitignored)
   - User-level: `~/.claude/settings.json` (all projects)

**Output of Step 1:**
- Clear understanding of hook purpose
- Risk assessment completed
- Event type identified
- Security concerns documented

**🛑 CHECKPOINT:** If risk level is HIGH, consider alternatives (Commands, manual operations).

---

### Step 2: Event Selection (5 min)

**Choose appropriate event:**

#### PreToolUse (Before Tool Execution)

**Use for:**
- Validation before action
- Lint before edit
- Check permissions
- Block unsafe operations

**Pros:**
- Can prevent bad operations
- Early validation
- Fail-fast

**Cons:**
- Can block legitimate operations
- Adds latency
- Must handle failures well

**Example use cases:**
```
✅ Lint file before Edit tool modifies it
✅ Check git status before Write creates files
✅ Validate no secrets before Bash runs commands
❌ Run tests (use PostToolUse instead)
```

#### PostToolUse (After Tool Success)

**Use for:**
- Run tests after changes
- Trigger builds
- Update documentation
- Log successful operations

**Pros:**
- Doesn't block operations
- Work already done
- Can verify success

**Cons:**
- Can't prevent bad operations
- May run after damage done
- Only fires on success

**Example use cases:**
```
✅ Run tests after Edit modifies code
✅ Trigger CI after git commit
✅ Update CHANGELOG after Write creates files
❌ Prevent bad commits (use PreToolUse)
```

#### UserPromptSubmit (Prompt Validation)

**Use for:**
- Validate user input
- Block sensitive data
- Enforce input policies
- Log user requests

**Pros:**
- Catch issues before processing
- Protect against data leaks
- Audit trail

**Cons:**
- High frequency (every prompt)
- Must be very fast
- Can be annoying if too restrictive

**Example use cases:**
```
✅ Check for API keys in prompts
✅ Enforce company policies
✅ Log all user interactions
❌ Complex analysis (too slow)
```

#### SessionStart / SessionEnd

**Use for:**
- Initialize environment
- Load context
- Cleanup resources
- Save state

**Pros:**
- One-time execution
- Good for setup/teardown

**Cons:**
- Delays session start
- Must be fast and reliable

**Example use cases:**
```
✅ Initialize git hooks on session start
✅ Clean temp files on session end
✅ Load environment variables
❌ Long-running setup (too slow)
```

**Decision:** Which event matches your use case?

---

### Step 3: Matcher Configuration (10 min)

**Design matcher pattern:**

The `matcher` field is a **regex pattern** that determines when the hook runs.

#### Matcher Best Practices

✅ **DO: Be specific**
```json
"matcher": "Edit"                    // Only Edit tool
"matcher": "Edit|Write"              // Edit or Write
"matcher": "Write.*\\.js$"           // Write tool for .js files
"matcher": "Bash.*git commit"        // Bash tool with git commit
```

❌ **DON'T: Be too broad**
```json
"matcher": ".*"                      // ❌ Matches everything (dangerous)
"matcher": ".*"                      // ❌ Runs on every event
```

#### Matcher Patterns

**Match specific tool:**
```json
"matcher": "Edit"
```

**Match multiple tools:**
```json
"matcher": "Edit|Write|Delete"
```

**Match tool with arguments:**
```json
"matcher": "Bash.*npm test"
"matcher": "Write.*package\\.json"
```

**Match file patterns:**
```json
"matcher": "Edit.*\\.ts$"           // TypeScript files
"matcher": "Write.*src/.*\\.py"     // Python files in src/
```

**Match all (use sparingly):**
```json
"matcher": ".*"                      // ⚠️ Use only if truly necessary
```

**⚠️ Security note:** Broad matchers increase attack surface. Be as specific as possible.

---

### Step 4: Command Design with Security Focus (20-30 min)

**Design safe command:**

#### Security Requirements (Mandatory)

**1. Variable Quoting**

❌ **NEVER DO THIS:**
```bash
"command": "eslint $FILE_PATH"           # ❌ Code injection
"command": "rm -rf $DIR_NAME"            # ❌ Dangerous
"command": "echo User said: $USER_INPUT" # ❌ Injection
```

✅ **ALWAYS DO THIS:**
```bash
"command": "eslint \"$FILE_PATH\""            # ✅ Quoted
"command": "[ -d \"$DIR\" ] && rm -rf \"$DIR\"" # ✅ Validated and quoted
"command": "echo \"User said: $USER_INPUT\""  # ✅ Quoted
```

**2. Input Validation**

✅ **Validate before use:**
```bash
# Check file exists and is in expected location
"command": "[ -f \"$FILE_PATH\" ] && [ \"$FILE_PATH\" = src/* ] && eslint \"$FILE_PATH\""

# Check variable is not empty
"command": "[ -n \"$VAR\" ] && process \"$VAR\""

# Validate format
"command": "echo \"$FILE\" | grep -qE '\\.(js|ts)$' && eslint \"$FILE\""
```

**3. Error Handling**

✅ **Handle failures:**
```bash
# Exit on error
"command": "set -e; eslint \"$FILE\" || exit 1"

# Trap errors
"command": "trap 'echo Hook failed >&2; exit 1' ERR; eslint \"$FILE\""

# Explicit checks
"command": "eslint \"$FILE\" && echo Success || echo Failed >&2"
```

**4. Principle of Least Privilege**

✅ **Minimal permissions:**
```bash
# Read-only where possible
"command": "cat \"$FILE\" | grep pattern"

# Avoid sudo
"command": "npm test"  # ✅ No elevated permissions

# Avoid dangerous commands
❌ "rm -rf /"
❌ "chmod 777 *"
❌ "curl | bash"
```

**5. Timeout Configuration**

✅ **Set appropriate timeout:**
```json
{
  "type": "command",
  "command": "npm test",
  "timeout": 120        // ✅ Reasonable for tests
}
```

**Timeout guidelines:**
- Quick validation: 5-10 seconds
- Linting: 10-30 seconds
- Testing: 60-180 seconds
- Builds: 120-300 seconds

#### Command Patterns (Secure)

**Pattern 1: Linting (PreToolUse)**
```json
{
  "type": "command",
  "command": "[ -f \"$FILE_PATH\" ] && eslint \"$FILE_PATH\" --max-warnings 0 || exit 0",
  "timeout": 30
}
```

**Pattern 2: Testing (PostToolUse)**
```json
{
  "type": "command",
  "command": "set -e; npm test -- \"$FILE_PATH\" 2>&1 | tee test-output.log",
  "timeout": 120
}
```

**Pattern 3: Validation (PreToolUse)**
```json
{
  "type": "command",
  "command": "git diff --cached --name-only | grep -qE '\\.(js|ts)$' && npm run lint:staged || exit 0",
  "timeout": 60
}
```

**Pattern 4: Logging (PostToolUse)**
```json
{
  "type": "command",
  "command": "echo \"[$(date)] Tool: $TOOL_NAME, File: $FILE_PATH\" >> .claude/audit.log",
  "timeout": 5
}
```

**Pattern 5: Cleanup (SessionEnd)**
```json
{
  "type": "command",
  "command": "[ -d \"/tmp/claude-temp\" ] && rm -rf \"/tmp/claude-temp\" && echo Cleaned || echo Nothing to clean",
  "timeout": 10
}
```

---

### Step 5: Complete Configuration (10 min)

**Generate JSON configuration:**

**Template:**
```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "regex-pattern",
        "hooks": [
          {
            "type": "command",
            "command": "safe-command-with-quoted-vars",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

**Complete Example: Pre-commit Linting Hook**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit.*\\.(js|ts)$",
        "hooks": [
          {
            "type": "command",
            "command": "set -e; [ -f \"$FILE_PATH\" ] && eslint \"$FILE_PATH\" --fix && echo \"✅ Lint passed: $FILE_PATH\" || { echo \"❌ Lint failed: $FILE_PATH\" >&2; exit 1; }",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Complete Example: Post-edit Testing Hook**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit.*src/.*\\.(js|ts)$",
        "hooks": [
          {
            "type": "command",
            "command": "npm test -- --findRelatedTests \"$FILE_PATH\" --passWithNoTests 2>&1 | tee .claude/test-output.log && echo \"✅ Tests passed\" || { echo \"⚠️ Tests failed, see .claude/test-output.log\" >&2; exit 0; }",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

**Complete Example: Sensitive Data Prevention**

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if the user prompt contains sensitive data like API keys (pattern: [A-Z0-9]{32,}), passwords, or secrets. Block if found. User prompt: $ARGUMENTS. Return JSON with {\"decision\": \"approve\" or \"block\", \"reason\": \"explanation\"}",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Write to configuration file:**

```bash
# Project-level (shared)
echo 'JSON configuration' > .claude/settings.json

# Project-level (personal)
echo 'JSON configuration' > .claude/settings.local.json

# User-level (all projects)
echo 'JSON configuration' > ~/.claude/settings.json
```

**⚠️ Important:** Validate JSON syntax before saving!

```bash
python3 -c "import json; json.load(open('.claude/settings.json'))" && echo "✅ Valid JSON" || echo "❌ Invalid JSON"
```

---

### Step 6: MANDATORY Security Review (15-30 min)

**🛑 CRITICAL: Complete this security checklist for EVERY Hook**

#### Security Checklist

**Variable Handling:**
- [ ] All variables quoted: `"$VAR"` not `$VAR`
- [ ] No variable substitution in command strings
- [ ] No `eval` or dynamic command generation
- [ ] No unvalidated user input execution

**Input Validation:**
- [ ] File paths validated (exists, correct location)
- [ ] Variables checked for empty/null
- [ ] File types validated (extensions, magic numbers)
- [ ] No path traversal vulnerabilities (`../` checks)

**Error Handling:**
- [ ] Uses `set -e` or equivalent
- [ ] `trap` for cleanup on error
- [ ] Explicit error checks for critical operations
- [ ] Failures are safe (fail-closed)

**Command Safety:**
- [ ] No dangerous commands (`rm -rf /`, `chmod 777`, etc.)
- [ ] No privilege escalation (`sudo`, `su`)
- [ ] No piping to shell (`curl | bash`)
- [ ] No hardcoded credentials
- [ ] No secrets in logs

**Timeout:**
- [ ] Timeout set appropriately
- [ ] Timeout < 600 seconds (10 min max)
- [ ] Considers worst-case execution time

**Principle of Least Privilege:**
- [ ] Minimal file access
- [ ] Read-only where possible
- [ ] No broad permissions
- [ ] Matcher as specific as possible

**Testing:**
- [ ] Tested in isolated environment
- [ ] Tested with malicious input
- [ ] Tested failure modes
- [ ] Tested timeout behavior

**Documentation:**
- [ ] Purpose clearly documented
- [ ] Security review completion recorded
- [ ] Known risks documented
- [ ] Mitigation strategies noted

**⚠️ IF ANY ITEM IS UNCHECKED: DO NOT DEPLOY**

---

### Step 7: Threat Modeling (10-15 min)

**Identify potential threats:**

For your Hook, consider:

**1. Code Injection**
- Could attacker control `$VAR` values?
- Are all variables properly quoted?
- Is there any `eval` or dynamic execution?

**2. Path Traversal**
- Could attacker use `../` to access unauthorized files?
- Are file paths validated?
- Is there allowlist for permitted paths?

**3. Denial of Service**
- Could hook run indefinitely?
- Is timeout set?
- Could malicious input cause hangs?

**4. Information Disclosure**
- Does hook log sensitive data?
- Could error messages leak information?
- Are credentials exposed?

**5. Privilege Escalation**
- Does hook use sudo?
- Could attacker gain elevated permissions?
- Are file permissions checked?

**6. Data Loss**
- Could hook delete important files?
- Is there backup/recovery?
- Are destructive operations protected?

**Mitigation strategies:**
- Quote all variables
- Validate all inputs
- Set timeouts
- Use principle of least privilege
- Test with malicious inputs
- Add logging and monitoring

---

### Step 8: Testing (15-30 min)

**Test thoroughly before deployment:**

#### Test 1: Basic Functionality

```bash
# Manually trigger event that should activate hook
# Verify hook executes as expected
# Check output/logs
```

#### Test 2: Error Handling

```bash
# Test with invalid input
# Test with missing files
# Test with timeout exceeded
# Verify failures are safe
```

#### Test 3: Security Testing

**Code injection attempt:**
```bash
# Try: FILE_PATH="; rm -rf /"
# Verify: Hook properly quotes and rejects
```

**Path traversal attempt:**
```bash
# Try: FILE_PATH="../../../etc/passwd"
# Verify: Hook validates and rejects
```

**Malicious input:**
```bash
# Try: USER_INPUT="$(malicious command)"
# Verify: Hook properly escapes
```

#### Test 4: Performance Testing

```bash
# Measure hook execution time
# Verify timeout is appropriate
# Check impact on workflow speed
```

#### Test 5: Integration Testing

```bash
# Test with actual workflow
# Verify doesn't block legitimate operations
# Check false positive rate
```

**Testing checklist:**
- [ ] Basic functionality works
- [ ] Error handling works
- [ ] Security tests pass
- [ ] Performance acceptable
- [ ] Integration smooth
- [ ] No false positives

---

### Step 9: Documentation and Deployment (10 min)

**Document the hook:**

**Create hook documentation:**

```markdown
## Claude Hooks

### [Hook Name]

**Event:** PreToolUse / PostToolUse / etc.
**Matcher:** `regex-pattern`
**Purpose:** [Clear description]
**Risk Level:** HIGH / MEDIUM / LOW

**Security Review:**
- Reviewed by: [Name]
- Date: [Date]
- Security checklist: ✅ Completed
- Known risks: [List any known risks]
- Mitigation: [How risks are mitigated]

**Configuration:** `.claude/settings.json` or `.claude/settings.local.json`

**Example:**
```json
[Hook configuration]
```

**Testing:**
- Tested: ✅
- Test date: [Date]
- Test results: [Summary]

**Monitoring:**
- Logs location: [Where logs are written]
- Failure handling: [What happens on failure]
```

**Deploy:**

```bash
# For project hooks (shared)
git add .claude/settings.json
git commit -m "Add [hook-name] hook for [purpose]

Security review completed: [date]
Reviewed by: [name]
Purpose: [description]
Risk level: [level]
"
git push
```

**Monitor:**
- Check logs after deployment
- Monitor for false positives
- Watch for failures
- Get team feedback

---

## Complete Hook Examples

### Example 1: Pre-commit Linter (Safe)

**Purpose:** Lint JavaScript/TypeScript files before editing

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit.*\\.(js|ts)$",
        "hooks": [
          {
            "type": "command",
            "command": "set -e; FILE=\"$FILE_PATH\"; [ -f \"$FILE\" ] || exit 0; eslint \"$FILE\" --max-warnings 0 && echo \"✅ Lint passed: $FILE\" || { echo \"❌ Lint failed: $FILE\" >&2; exit 1; }",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Security notes:**
- ✅ Variables quoted
- ✅ File existence checked
- ✅ Error handling present
- ✅ Timeout set appropriately
- ✅ Specific matcher (not `.*`)
- ✅ Read-only operation (safe)

---

### Example 2: Post-edit Test Runner (Safe)

**Purpose:** Run tests after editing source files

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit.*src/.*\\.(js|ts)$",
        "hooks": [
          {
            "type": "command",
            "command": "FILE=\"$FILE_PATH\"; npm test -- --findRelatedTests \"$FILE\" --passWithNoTests --silent 2>&1 | tail -20 && echo \"✅ Tests passed for $FILE\" || echo \"⚠️ Some tests failed\" >&2",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

**Security notes:**
- ✅ Variables quoted
- ✅ Output limited (tail -20)
- ✅ Non-blocking (doesn't exit on failure)
- ✅ Appropriate timeout
- ✅ Specific path pattern

---

### Example 3: Sensitive Data Detector (Prompt Hook)

**Purpose:** Prevent API keys in prompts

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Analyze the user prompt for sensitive data: API keys (patterns: sk-[A-Za-z0-9]{20,}, [A-Z0-9]{32,}), passwords, private keys, secrets. If found, respond with JSON: {\"decision\": \"block\", \"reason\": \"Found potential API key\"}. Otherwise: {\"decision\": \"approve\", \"reason\": \"No sensitive data\"}. Prompt to analyze: $ARGUMENTS",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Security notes:**
- ✅ Uses prompt type (LLM evaluation)
- ✅ Fast timeout (10s)
- ✅ Blocks on detection
- ⚠️ Broad matcher (necessary for this use case)
- ⚠️ Make sure LLM doesn't log sensitive data

---

### Example 4: Session Cleanup (Safe)

**Purpose:** Clean temporary files on session end

```json
{
  "hooks": {
    "SessionEnd": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "TMPDIR=\"/tmp/claude-session-$SESSION_ID\"; [ -d \"$TMPDIR\" ] && rm -rf \"$TMPDIR\" && echo \"Cleaned $TMPDIR\" || echo \"No cleanup needed\"",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Security notes:**
- ✅ Variables quoted
- ✅ Directory existence checked
- ✅ Specific path (not broad rm -rf)
- ✅ Short timeout
- ✅ Logging present

---

## Common Patterns and Anti-Patterns

### Safe Patterns ✅

**Pattern 1: Validation with early exit**
```bash
"[ -f \"$FILE\" ] && validate \"$FILE\" || exit 0"
```

**Pattern 2: Error handling with trap**
```bash
"trap 'echo Failed >&2; exit 1' ERR; process \"$FILE\""
```

**Pattern 3: Timeout-safe operations**
```bash
"timeout 30 eslint \"$FILE\" || echo Timeout"
```

**Pattern 4: Logging without secrets**
```bash
"echo \"[$(date)] Processed: $(basename \"$FILE\")\" >> hook.log"
```

### Dangerous Anti-Patterns ❌

**Anti-pattern 1: Unquoted variables**
```bash
"eslint $FILE"  # ❌ Code injection
```

**Anti-pattern 2: Eval or dynamic execution**
```bash
"eval \"$COMMAND\""  # ❌ Arbitrary code execution
```

**Anti-pattern 3: Broad destructive operations**
```bash
"rm -rf $DIR"  # ❌ Dangerous
"rm -rf *"     # ❌ Very dangerous
```

**Anti-pattern 4: No error handling**
```bash
"process_file $FILE"  # ❌ No validation, no error handling
```

**Anti-pattern 5: Hardcoded secrets**
```bash
"curl -H 'Authorization: Bearer sk-abc123' ..."  # ❌ Exposed secret
```

**Anti-pattern 6: Privilege escalation**
```bash
"sudo rm -rf /var/log/*"  # ❌ Elevated permissions
```

---

## Best Practices Summary

### Security (MANDATORY)

✅ **ALWAYS:**
- Quote all variables: `"$VAR"`
- Validate input before use
- Set appropriate timeout
- Use `set -e` or explicit error checks
- Test with malicious input
- Complete security checklist
- Document security review

❌ **NEVER:**
- Unquoted variables: `$VAR`
- `eval` or dynamic execution
- Hardcoded credentials
- Broad destructive operations (`rm -rf /`)
- Privilege escalation (`sudo`)
- Skip security review

### Reliability

✅ **DO:**
- Handle errors gracefully
- Log appropriately
- Test failure modes
- Set realistic timeouts
- Monitor after deployment

❌ **DON'T:**
- Assume commands always succeed
- Use infinite loops
- Ignore errors
- Skip testing

### Usability

✅ **DO:**
- Keep hooks fast
- Provide clear error messages
- Make matchers specific
- Document purpose
- Consider false positives

❌ **DON'T:**
- Block legitimate operations
- Make hooks too slow
- Use overly broad matchers
- Skip documentation

---

## Troubleshooting

### Issue: Hook not triggering

**Symptoms:** Event occurs but hook doesn't run

**Solutions:**
- Check matcher pattern (regex correct?)
- Verify event name (correct capitalization?)
- Check JSON syntax (valid?)
- Restart Claude session
- Check logs for errors

### Issue: Hook fails with permission error

**Symptoms:** "Permission denied"

**Solutions:**
- Check file permissions
- Don't use `sudo` (not supported)
- Verify Claude has access to files
- Check file paths are correct

### Issue: Hook times out

**Symptoms:** Hook killed after timeout

**Solutions:**
- Increase timeout (if operation legitimately slow)
- Optimize command (make it faster)
- Check for infinite loops
- Consider if hook is appropriate for event

### Issue: Hook blocks legitimate operations

**Symptoms:** False positives blocking work

**Solutions:**
- Refine matcher to be more specific
- Adjust validation logic
- Add exceptions for known cases
- Consider PostToolUse instead of PreToolUse

---

## Success Criteria

A well-built Hook has:
- ✅ Passed complete security checklist
- ✅ Appropriate event selection
- ✅ Specific matcher (not overly broad)
- ✅ Properly quoted variables
- ✅ Input validation
- ✅ Error handling
- ✅ Appropriate timeout
- ✅ Tested thoroughly (including security tests)
- ✅ Documented with security review details
- ✅ Monitored after deployment
- ✅ Team-reviewed (for project hooks)

---

## Final Security Reminder

**🛑 CRITICAL: Before deploying ANY Hook:**

1. Complete the security checklist
2. Perform threat modeling
3. Test with malicious input
4. Document security review
5. Get peer review for HIGH risk hooks
6. Monitor after deployment

**Hooks execute arbitrary commands automatically. You are responsible for ensuring they are safe.**

---

## Next Steps

After creating the hook:
1. Monitor logs for unexpected behavior
2. Watch for false positives/negatives
3. Gather team feedback
4. Iterate and improve
5. Consider additional hooks for related workflows

**Need validation?** Use the `artifact-validator` Skill to check hook configuration.

**Unsure if Hook is right?** Use the `artifact-advisor` Skill to confirm artifact type choice.

**Want more automation?** Use `/suggest-artifacts` to identify opportunities.
