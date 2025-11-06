# Artifact Type Decision Template

Use this template to systematically analyze user requirements and recommend the appropriate Claude Code artifact type.

---

## User Request

**Original request:**
[Paste user's request here]

---

## Requirements Analysis

### 1. Purpose
**What does the user want to achieve?**
- [ ] Brief description:

### 2. Invocation Model
**How should this be triggered?**
- [ ] Automatic (Claude detects when to use)
- [ ] Manual (user explicitly invokes)
- [ ] Event-driven (responds to specific events)
- [ ] Delegated (main agent assigns sub-task)

### 3. Reasoning Requirements
**Does this need reasoning or is it execution/validation?**
- [ ] Reasoning (analysis, recommendations, decision-making)
- [ ] Execution (running commands, scripting)
- [ ] Validation (checking rules, enforcing policies)

### 4. Complexity
**How complex is the workflow?**
- [ ] Simple (1-2 steps, quick)
- [ ] Medium (multi-step, some methodology)
- [ ] High (extensive methodology, multiple approaches)

### 5. Context Needs
**Does this need special context handling?**
- [ ] Extended methodology (benefits from separate context)
- [ ] Isolated context (keeps main conversation clean)
- [ ] Integrated (works in main conversation)
- [ ] None (no context, just execution)

### 6. Additional Factors
- [ ] Team-shared workflow
- [ ] High-stakes operation (needs deliberate trigger)
- [ ] Security-sensitive
- [ ] Frequent/repeated use
- [ ] Benefits from examples and templates

---

## Decision Matrix Application

Based on requirements analysis, check which characteristics match:

### Skill Characteristics
- [ ] Automatic activation (Claude detects)
- [ ] Requires reasoning or domain expertise
- [ ] Benefits from methodology and examples
- [ ] User naturally mentions trigger keywords
- [ ] Medium to high complexity

**Score:** ___/5

### Command Characteristics
- [ ] User should explicitly trigger
- [ ] Standardizes team workflow
- [ ] Simple to medium complexity
- [ ] Benefits from arguments
- [ ] Repeatable process

**Score:** ___/5

### Subagent Characteristics
- [ ] Main agent delegates sub-task
- [ ] Complex analysis or processing
- [ ] Benefits from isolated context
- [ ] Specialized system prompt needed
- [ ] Autonomous task completion

**Score:** ___/5

### Hook Characteristics
- [ ] Event-driven automation
- [ ] Simple validation or execution
- [ ] Can approve/block operations
- [ ] No reasoning required
- [ ] Policy enforcement

**Score:** ___/5

---

## Recommendation

### Primary Recommendation: [ARTIFACT TYPE]

**Score:** [X/5] - [Highest scoring type]

### Justification
**Why this artifact type?**
1. [Key reason 1 based on requirements]
2. [Key reason 2 based on requirements]
3. [Key reason 3 based on requirements]

### Key Benefits for This Use Case
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

### Example Similar Use Cases
- [Similar example 1]
- [Similar example 2]
- [Similar example 3]

### Anti-Pattern Avoided
❌ [Wrong artifact type]: [Why it would be wrong]
✅ [Recommended type]: [Why it's right]

---

## Alternative Considerations

### Alternative 1: [If applicable]
**Score:** [X/5]
**When to consider:** [Scenario where this would be better]

### Alternative 2: [If applicable]
**Score:** [X/5]
**When to consider:** [Scenario where this would be better]

---

## Implementation Guidance

### What to Create

**File structure:**
```
[File paths and structure]
```

**Key configuration:**
```yaml/json
[Sample configuration]
```

**Description pattern:**
```
[Recommended description with trigger keywords]
```

### Creation Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]
4. [Step 4]
5. [Step 5]

### Tools/Resources Needed
- Use [creation skill/tool] for guidance
- Reference [specification context]
- Follow [workflow context]
- Validate with [validation tool]

---

## Security Considerations

**For Hooks only:**
- [ ] Security review required
- [ ] Input validation needed
- [ ] Path traversal prevention
- [ ] No credential access
- [ ] Timeout configured
- [ ] Peer review scheduled

---

## Next Steps

### Immediate Actions
1. [Action 1]
2. [Action 2]
3. [Action 3]

### Success Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

### Follow-up Questions (if any)
- [Question 1 to clarify further]
- [Question 2 to refine recommendation]

---

## Template Usage Notes

**How to use this template:**
1. Fill out Requirements Analysis section first
2. Score each artifact type based on characteristics
3. Highest score indicates best fit
4. If scores are close (within 1 point), consider alternatives
5. If all scores low (<3), might not need artifact or need clarification
6. Always provide justification, not just the score

**Edge cases:**
- **Tied scores:** Ask clarifying questions about priorities
- **Low scores overall:** Consider if artifact is needed at all
- **Multiple high scores:** May need combination of artifact types
- **Unclear requirements:** Fill in gaps with clarifying questions

---

**This template ensures systematic, consistent artifact type recommendations.**
