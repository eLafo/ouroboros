# Ouroboros Plugin 🐍

> **Create Claude Code artifacts using Claude Code artifacts themselves** - A meta/recursive system for building Skills, Commands, Subagents, and Hooks.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## What is This?

The **Ouroboros Plugin** is a comprehensive toolkit for creating and managing Claude Code artifacts. Named after the ancient symbol of a serpent eating its own tail, this plugin uses Claude Code's own extensibility features to help you build more Claude Code extensions.

Think of it as **"artifacts that help you create artifacts"** - a self-referential system that makes extending Claude Code easier, safer, and more consistent.

## Why "Ouroboros"?

The [Ouroboros](https://en.wikipedia.org/wiki/Ouroboros) (serpent eating its tail) represents self-reference and recursion - perfect for a plugin that uses Claude Code artifacts to help you build Claude Code artifacts. It's meta all the way down! 🐍

## What Can It Do?

This plugin provides **expert guidance** for:

### 🏗️ **Creating Artifacts**
- **Skills** - Automatically-activated AI capabilities
- **Commands** - User-triggered workflow shortcuts
- **Subagents** - Specialized AI assistants with isolated contexts
- **Hooks** - Event-driven automation scripts
- **Auto-updating** - All builders verify latest specs before creating

### ✅ **Validating Quality**
- Check YAML syntax and structure
- Validate naming conventions
- Grade artifact quality (A-F scoring)
- Identify security issues (especially for Hooks)

### 🎯 **Making Decisions**
- Choose the right artifact type for your needs
- Understand trade-offs between approaches
- Get recommendations based on your workflow

### 📚 **Finding Information**
- Access complete specifications and best practices
- Fetch official Claude Code documentation
- Learn from comprehensive examples
- **Real-time spec verification** via `claude-researcher`

### 🔍 **Analyzing Workflows**
- Identify automation opportunities
- Suggest specific artifacts to build
- Prioritize by impact and effort

---

## Quick Start

### Installation

1. **Clone or download this plugin:**
   ```bash
   cd ~/.claude/plugins
   git clone <repository-url> ouroboros
   ```

2. **Restart Claude Code** to load the plugin

3. **Try it out:**
   ```bash
   /suggest-artifacts
   ```

### Your First Artifact

**Example: Creating a Skill**

```bash
# Explicit invocation
/build-skill pdf-processor

# Or just ask naturally, and skill-builder activates automatically:
"I want to create a Skill for processing PDF files"
```

Claude will guide you through:
0. **Documentation verification** (checks latest official specs)
1. Planning and requirements gathering
2. **Description crafting** (most critical - determines activation)
3. Directory setup
4. YAML configuration
5. Content development
6. Validation
7. **Activation testing** (ensures it works)
8. Documentation

---

## Available Tools

### 📋 Commands (Explicit Invocation)

Use these by typing `/command-name`:

#### Builder Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/build-skill` | Create a new Skill | Build automatic AI capabilities |
| `/build-command` | Create a new Command | Build user-triggered workflows |
| `/build-subagent` | Create a new Subagent | Build specialized AI assistants |
| `/build-hook` | Create a new Hook | Build event-driven automation (⚠️ requires security review) |

#### Analysis Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/suggest-artifacts` | Analyze workflow and suggest what to build | Starting point - "What should I automate?" |
| `/plugin-health-check` | Validate all artifacts and generate quality report | Before deployment, after changes |

#### Maintenance Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/sync-readme` | Sync README.md with current project state and artifact inventory | After adding/removing artifacts, before releases |

---

### 🎓 Skills (Automatic Activation)

These activate automatically when Claude detects you need them:

#### Builder Skills

| Skill | Activates When | Purpose |
|-------|----------------|---------|
| **skill-builder** | You mention creating/building Skills | Guides Skill creation with activation testing |
| **command-builder** | You mention creating/building Commands | Guides Command creation with argument handling |
| **subagent-builder** | You mention creating/building Subagents | Guides Subagent creation with task delegation |
| **hook-builder** | You mention creating/building Hooks | Guides Hook creation with **mandatory security review** |

#### Validation & Migration

| Skill | Activates When | Purpose |
|-------|----------------|---------|
| **artifact-validator** | You ask to validate/check artifact quality | Validates and grades artifacts (A-F scoring) |
| **artifact-migrator** | You mention updating/fixing broken artifacts | Helps migrate artifacts to new specifications |
| **artifact-advisor** | You're unsure which artifact type to use | Helps choose between Skills/Commands/Subagents/Hooks |

#### Knowledge & Research

| Skill | Activates When | Purpose |
|-------|----------------|---------|
| **claude-expert** | You need specifications or technical details | Provides complete artifact specifications |
| **claude-researcher** | You ask about Claude Code features/docs | Fetches official documentation from code.claude.com |

---

## How to Use

### Starting from Scratch: "What should I build?"

```bash
/suggest-artifacts
```

Claude analyzes your workflow and suggests 2-4 specific artifacts with:
- Priority (HIGH/MEDIUM/LOW impact)
- What it does
- Example usage
- Benefits

**Example output:**
```
Based on your workflow, here are artifacts that could help:

## 1. Pre-Commit Linter Hook (HIGH IMPACT)
Automatically lint code before commits to catch issues early.

## 2. API Testing Skill (MEDIUM IMPACT)
Automatically help with REST API testing and validation.

Would you like help creating #1?
```

---

### Creating Artifacts

#### Option 1: Use Builder Commands (Explicit)

**Consistent `/build-X` interface:**

```bash
/build-skill pdf-processor
/build-command deploy
/build-subagent code-reviewer
/build-hook pre-commit-validator
```

#### Option 2: Ask Naturally (Automatic)

Just describe what you want, and the appropriate builder Skill activates:

```
"I want to create a Skill for analyzing SQL queries"
→ skill-builder activates

"Help me create a command for deploying"
→ command-builder activates

"I need a subagent for code analysis"
→ subagent-builder activates

"Create a pre-commit hook for testing"
→ hook-builder activates (with security review)

"Which artifact type should I use for deployment?"
→ artifact-advisor activates

"My skill stopped activating"
→ artifact-migrator activates
```

---

### Complete Example Workflow

**Scenario: You want to automate code linting**

1. **Discover what to build:**
   ```bash
   /suggest-artifacts testing
   ```
   *Suggests: "Pre-commit Linter Hook"*

2. **Confirm it's the right type:**
   ```
   "Should I use a Hook or Command for linting before commits?"
   ```
   *artifact-advisor explains: Hook is correct for automatic enforcement*

3. **Create the Hook:**
   ```bash
   /build-hook pre-commit-linter
   ```
   *Guides through 9-step workflow with security review*

4. **Validate quality:**
   ```
   "Validate my pre-commit-linter Hook"
   ```
   *artifact-validator checks and grades it*

5. **Check overall plugin health:**
   ```bash
   /plugin-health-check
   ```
   *Shows all artifacts, scores, and recommendations*

---

## Architecture

The plugin uses **progressive disclosure** - information is loaded only when needed to keep context usage efficient.

### Directory Structure

```
ouroboros/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata
├── commands/
│   ├── suggest-artifacts.md    # Workflow analyzer
│   ├── skill.md                 # CRUD operations for Skills
│   ├── command.md               # CRUD operations for Commands
│   ├── agent.md                 # CRUD operations for Subagents
│   ├── hook.md                  # CRUD operations for Hooks
│   ├── plugin-health-check.md  # Quality dashboard
│   └── sync-readme.md           # README synchronization
└── skills/
    ├── artifact-advisor/        # Choose artifact type
    ├── artifact-migrator/       # Migrate/update artifacts (NEW)
    ├── artifact-validator/      # Validate quality
    ├── claude-expert/           # Specifications & knowledge
    │   ├── SKILL.md
    │   ├── specifications.md
    │   ├── comparison-matrix.md
    │   └── common-mistakes.md
    ├── claude-researcher/       # Fetch official docs
    ├── command-builder/         # Guide Command creation (NEW)
    ├── hook-builder/            # Guide Hook creation with security (NEW)
    ├── skill-builder/           # Guide Skill creation
    └── subagent-builder/        # Guide Subagent creation (NEW)
```

### How It Works

**Multi-artifact activation:**
- Multiple Skills can activate simultaneously
- Commands integrate with Skills seamlessly
- Skills reference each other's knowledge

**Example:**
```
User: "I want to create a Skill for PDF processing"

Activates:
- skill-builder (guides creation)
- claude-researcher (fetches latest Skills specs)
- claude-expert (provides specifications)

Result: Comprehensive guidance with current specs
```

**Step 0: Documentation Verification**
All builder commands now start by:
1. Using `claude-researcher` to fetch latest official documentation
2. Verifying current YAML schemas and field requirements
3. Checking for recent specification changes
4. Ensuring workflows use most current standards

This means every artifact you create is built with up-to-date Claude Code specifications!

**Complete Builder Suite:**
All four artifact types now have automatic builder activation:
- **skill-builder** → Creates Skills with activation testing
- **command-builder** → Creates Commands with argument handling
- **subagent-builder** → Creates Subagents with task delegation
- **hook-builder** → Creates Hooks with mandatory security review

---

## Key Concepts

### Artifact Types (Quick Reference)

| Type | Invocation | Context | Use Case |
|------|------------|---------|----------|
| **Skill** | Automatic (Claude decides) | Shared with main | Specialized knowledge, auto-activation |
| **Command** | Explicit (`/command`) | Shared with main | Repeatable workflows, user control |
| **Subagent** | Delegated (Task tool) | Isolated | Complex reasoning, context isolation |
| **Hook** | Event-driven | Background | Automation, validation, enforcement |

### When to Use What?

**Use a Skill when:**
- Users will naturally describe the need
- You want automatic activation
- Task requires specialized methodology
- Example: "PDF processor", "API tester"

**Use a Command when:**
- User wants explicit control
- Workflow is triggered on-demand
- Team needs shared, standardized process
- Example: "Deploy to staging", "Run tests"

**Use a Subagent when:**
- Task requires deep focus/reasoning
- Benefits from context isolation
- Analysis would pollute main context
- Example: "Code reviewer", "Security auditor"

**Use a Hook when:**
- Automation should be automatic
- Enforcement of policies needed
- Integration with external systems
- ⚠️ Example: "Pre-commit linter", "Test runner"

*See `artifact-advisor` for detailed decision framework*

---

## Quality Framework

All artifacts are validated using the **Q = 0.40R + 0.30C + 0.20S + 0.10E** framework:

- **R (Relevance)**: How well does it serve its purpose?
- **C (Completeness)**: Is everything present?
- **S (Specification)**: Does it follow standards?
- **E (Efficiency)**: Is it optimized?

**Grades:**
- **A (90-100%)**: Excellent, production-ready
- **B (80-89%)**: Good, minor improvements
- **C (70-79%)**: Acceptable, needs work
- **D (60-69%)**: Below standard, significant issues
- **F (<60%)**: Fails validation, cannot be used

Run `/plugin-health-check` to see quality scores across all your artifacts.

---

## Examples

### Example 1: Building a PDF Processor Skill

```bash
/build-skill pdf-processor
```

**Claude guides you through:**

0. **Documentation verification**: Fetches latest Skills specifications from code.claude.com

1. **Planning**: What operations? (extract text, merge PDFs, convert formats)

2. **Description crafting** (critical!):
   ```yaml
   description: Extract text, images, and tables from PDFs. Fill PDF forms,
   merge documents, convert between formats (PDF↔Word). Use when working
   with PDF files or document processing tasks.
   ```

3. **Directory setup**: `.claude/skills/pdf-processor/`

4. **YAML frontmatter**:
   ```yaml
   ---
   name: pdf-processor
   description: [crafted above]
   allowed-tools: Bash, Read, Write
   ---
   ```

5. **Content development**: Step-by-step methodology, examples

6. **Validation**: YAML syntax, naming, description quality

7. **Activation testing**:
   ```
   Test: "Extract tables from quarterly-report.pdf"
   Expected: ✅ pdf-processor activates
   ```

8. **Documentation**: Add to README, commit to git

**Result**: A production-ready Skill that activates when users mention PDFs!

---

### Example 2: Deployment Command

```bash
/build-command deploy
```

**Creates:**

```markdown
---
description: Deploy application to specified environment with validation
argument-hint: <environment> [branch]
allowed-tools: Bash, Read
disable-model-invocation: true
---

Deploy to $1 environment from branch ${2:-main}.

## Pre-Deployment Validation
1. Verify git status is clean
2. Check branch exists
3. Confirm environment is valid
4. Run pre-deployment tests

## Deployment Steps
```bash
git checkout ${2:-main}
git pull origin ${2:-main}
npm run build:$1
npm run deploy:$1
npm run verify:$1
```

## Post-Deployment
1. Run smoke tests
2. Verify deployment health
3. Report deployment status
```

**Usage:**
```bash
/deploy staging feature-123  # Deploy feature-123 to staging
/deploy production           # Deploy main to production (with confirmation)
```

---

### Example 3: Security Hook (with mandatory security review)

```bash
/build-hook pre-commit-linter
```

**Claude emphasizes security** (10+ warnings):

⚠️ **SECURITY CHECKLIST:**
- [ ] Variables quoted: `"$VAR"`
- [ ] Input validation present
- [ ] Error handling exists
- [ ] No hardcoded secrets
- [ ] Timeout set appropriately
- [ ] Tested with malicious input
- [ ] Peer reviewed

**Generates secure Hook:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit.*\\.(js|ts)$",
        "hooks": [
          {
            "type": "command",
            "command": "set -e; FILE=\"$FILE_PATH\"; [ -f \"$FILE\" ] || exit 0; eslint \"$FILE\" --max-warnings 0 && echo \"✅ Lint passed\" || { echo \"❌ Lint failed\" >&2; exit 1; }",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Safety features:**
- Variables properly quoted
- File existence checked
- Error handling with `set -e`
- Appropriate timeout
- Exits safely on failure

---

## Best Practices

### For Skills

✅ **DO:**
- Spend 10-15 minutes crafting description (most critical!)
- Include 10+ trigger keywords
- Test activation with 5 different phrasings
- Provide 3-5 concrete examples
- Restart Claude after creating

❌ **DON'T:**
- Rush through description ("helps with PDFs")
- Skip activation testing
- Use vague language
- Forget to restart Claude

### For Commands

✅ **DO:**
- Use clear, descriptive names
- Provide argument hints
- Restrict tools when appropriate
- Add `disable-model-invocation: true` for destructive ops
- Document in README

❌ **DON'T:**
- Make command names cryptic (`/dp` vs `/deploy`)
- Give excessive permissions
- Allow model invocation for dangerous operations

### For Subagents

✅ **DO:**
- Define clear, focused specialization
- Provide detailed system prompts
- Use principle of least privilege (tools)
- Include step-by-step methodology
- Specify output format

❌ **DON'T:**
- Make subagents too general-purpose
- Skip methodology details
- Give excessive tool access

### For Hooks

⚠️ **CRITICAL - ALWAYS:**
- Complete security checklist (mandatory!)
- Quote ALL variables: `"$VAR"`
- Validate input before use
- Set appropriate timeout
- Test with malicious input
- Get peer review for HIGH risk
- Document security review completion

❌ **NEVER:**
- Unquoted variables: `$VAR`
- Skip security review
- Use `eval` or dynamic execution
- Hardcode credentials

---

## Workflow Analysis

The `/suggest-artifacts` command analyzes your development patterns and suggests what to build.

**Common patterns it recognizes:**

| You mention | It suggests |
|------------|-------------|
| "I work with PDFs frequently" | PDF Processor Skill |
| "I test APIs daily" | API Testing Skill + Commands |
| "I forget to run tests before commits" | Pre-Commit Test Hook |
| "Deployment is manual and error-prone" | Deployment Command (explicit control) |
| "Need thorough code reviews" | Code Review Subagent (isolated analysis) |
| "I debug by analyzing logs" | Log Analyzer Skill |
| "SQL queries are slow" | SQL Optimizer Skill |

**Try it:**
```bash
/suggest-artifacts           # General analysis
/suggest-artifacts testing   # Focus on testing workflows
/suggest-artifacts git       # Focus on git workflows
```

---

## Plugin Health Monitoring

Use `/plugin-health-check` to validate all your artifacts:

```bash
/plugin-health-check
```

**Generates comprehensive report:**

```
Plugin Health Report
Generated: 2025-11-06

Overall Health: B (84%)

Artifact Count:
- Skills: 5 (4 project, 1 user)
- Commands: 3 (3 project)
- Subagents: 2 (2 project)
- Hooks: 1 (1 project)

Quality Distribution:
- Grade A: 7 artifacts
- Grade B: 3 artifacts
- Grade C: 1 artifact

Critical Issues: 0
Warnings: 2
Recommendations: 5

[Detailed findings with specific fix recommendations...]
```

**When to run:**
- Before every release
- After adding/modifying artifacts
- Monthly for active development
- When troubleshooting issues

---

## Troubleshooting

### "My Skill never activates"

**Cause:** Description doesn't contain keywords users actually mention

**Fix:**
1. Use `artifact-migrator` Skill: "My skill stopped activating"
2. Add more specific trigger keywords to description
3. Test with explicit technology mentions
4. Restart Claude session
5. Or regenerate with `skill-builder`

**Example:**
```yaml
# Before (too vague)
description: Helps with APIs

# After (specific)
description: Test REST APIs by sending HTTP requests, validating responses,
checking status codes. Use for API testing, debugging HTTP services.
```

### "Command not found"

**Cause:** File not in correct location or Claude not restarted

**Fix:**
1. Verify file: `.claude/commands/command-name.md`
2. Check file has `.md` extension
3. Restart Claude session
4. Try `/help` to see if command appears

### "Hook keeps failing"

**Cause:** Variables not quoted, invalid paths, or timeout issues

**Fix:**
1. Check ALL variables are quoted: `"$VAR"`
2. Validate file paths exist
3. Increase timeout if needed
4. Review security checklist
5. Test in isolation first

### "artifact-validator says Grade F"

**Cause:** YAML syntax error, missing fields, or poor description

**Fix:**
1. Validate YAML syntax: `python3 -c "import yaml; yaml.safe_load(...)"`
2. Check required fields present
3. Verify name matches directory
4. Improve description with more trigger keywords
5. Run `/build-X` to regenerate properly

---

## Advanced Usage

### Creating Artifact Families

Group related artifacts for comprehensive coverage:

**Example: Testing Suite**
- **Skill**: `test-helper` (automatic guidance)
- **Command**: `/run-tests` (explicit test execution)
- **Subagent**: `test-generator` (generate comprehensive tests)
- **Hook**: Pre-commit test validation

### Progressive Disclosure with Skills

Large Skills can include supporting files:

```
.claude/skills/data-analyst/
├── SKILL.md              # Main entry point
├── examples.md           # Extended examples
├── reference.md          # API reference
└── templates/
    ├── analysis.py
    └── visualization.py
```

**SKILL.md** loads first. Supporting files loaded only when needed.

### Chaining Subagents

Main agent can delegate to multiple subagents:

```
User: "Analyze this codebase thoroughly"

Main agent:
1. Delegates to code-reviewer (quality analysis)
2. Delegates to security-auditor (security analysis)
3. Delegates to test-generator (coverage analysis)
4. Synthesizes all results
```

---

## Contributing

### Adding New Artifacts to This Plugin

1. **Skills**: Add to `skills/[skill-name]/SKILL.md`
2. **Commands**: Add to `commands/[command-name].md`
3. **Update documentation**: Add to this README
4. **Test**: Use `artifact-validator` to check quality
5. **Health check**: Run `/plugin-health-check`

### Quality Standards

All artifacts in this plugin maintain:
- **Grade A** (≥90%) quality scores
- Complete documentation
- 3+ concrete examples
- Tested activation/execution
- Security review (for Hooks)

---

## Roadmap

See [ARTIFACT-ROADMAP.md](ARTIFACT-ROADMAP.md) for planned enhancements.

**Recently Completed (v0.2.0):**
- ✅ Full builder command suite (`/build-X` for all types)
- ✅ **Complete builder Skills suite** (automatic activation for all 4 types)
  - `command-builder` - Commands with argument handling
  - `subagent-builder` - Subagents with task delegation
  - `hook-builder` - Hooks with mandatory security review
  - `artifact-migrator` - Migration and update support
- ✅ Plugin health dashboard
- ✅ Workflow analysis and suggestions
- ✅ Comprehensive validation framework
- ✅ **Auto-updating documentation verification** (Step 0 in all commands)
  - All builder commands now fetch latest official specs from code.claude.com
  - Ensures workflows always use current Claude Code standards
  - Uses `claude-researcher` skill for real-time documentation checks

**Future possibilities:**
- Artifact templates library
- Interactive creation wizard
- Version management tools
- Team collaboration features
- Automated testing framework

---

## FAQ

### Q: Should I use the Skill or Command version of builders?

**A:** Both work identically! Choose based on preference:
- **Skill** (e.g., `skill-builder`): Activates automatically when you naturally describe wanting to create something
  - Example: "Help me create a command" → `command-builder` activates
- **Command** (e.g., `/build-command`): Explicit control, direct invocation
  - Example: `/build-command deploy`

**All four artifact types have both:**
- Skills: `skill-builder`, `command-builder`, `subagent-builder`, `hook-builder`
- Commands: `/build-skill`, `/build-command`, `/build-subagent`, `/build-hook`

Command versions provide predictable invocation, while Skills activate contextually.

### Q: How do I know which artifact type to use?

**A:** Three ways:
1. Ask the `artifact-advisor` Skill: "Should I use a Skill or Command for X?"
2. Run `/suggest-artifacts` to get recommendations
3. See "Key Concepts" section above for quick reference

### Q: Are Hooks safe?

**A:** Hooks execute arbitrary commands and require careful security review. The `/build-hook` command includes **mandatory security checklists** and emphasizes safe patterns. Never skip the security review!

### Q: Can I use this plugin to manage other plugins?

**A:** Yes! The Ouroboros Plugin can help you create and validate artifacts for any Claude Code plugin, not just itself. It's a meta-tool for the entire Claude Code ecosystem.

### Q: How do I update existing artifacts?

**A:** Use the new `artifact-migrator` Skill!
1. Ask naturally: "My skill stopped working" or "Update my outdated command"
2. `artifact-migrator` detects issues and suggests fixes
3. Creates backups before making changes
4. Validates after migration

Or manually:
1. Use `artifact-validator` to identify issues
2. Edit the artifact files directly
3. Run `/plugin-health-check` to verify improvements
4. Test activation/execution after changes

### Q: Does this work with MCP servers?

**A:** Yes! Artifacts can use MCP tools. For example:
```yaml
allowed-tools: Read, Write, mcp__github__create_issue
```

---

## Resources

### Official Documentation

- [Claude Code Documentation](https://code.claude.com/docs)
- Use `claude-researcher` Skill to fetch latest docs automatically

### Plugin Documentation

- [ARTIFACT-ROADMAP.md](ARTIFACT-ROADMAP.md) - Planned features and completion status
- [CLAUDE.md](CLAUDE.md) - Technical guidance for Claude Code when working with this plugin
- `skills/claude-expert/` - Complete technical specifications

### Learning Path

**Beginner:**
1. Start with `/suggest-artifacts` to identify opportunities
2. Create your first artifact with a `/build-X` command
3. Validate with `artifact-validator`

**Intermediate:**
4. Create artifact families (related Skills/Commands/Subagents)
5. Use `/plugin-health-check` regularly
6. Understand progressive disclosure with supporting files

**Advanced:**
7. Create Hooks with comprehensive security review
8. Design complex Subagents with isolation strategies
9. Contribute new artifacts to this plugin

---

## License

MIT License - See LICENSE file for details

---

## Credits

Created by the Claude Code community to make extending Claude Code easier, safer, and more consistent.

**Philosophy:** Use Claude Code's own extensibility features to improve Claude Code's extensibility. It's meta. It's recursive. It's the Ouroboros. 🐍

---

## Support

**Found a bug?** Use the artifact-validator to check your artifacts first.

**Need help?**
- Ask Claude naturally: "How do I create a Skill?"
- Use `artifact-advisor` for decisions
- Use `claude-researcher` for official docs
- Run `/plugin-health-check` for diagnostics

**Want to contribute?** Create artifacts for this plugin using the plugin itself! That's the Ouroboros way. 🐍

---

**Happy artifact building! 🚀**
