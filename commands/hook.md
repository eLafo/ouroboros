---
description: Manage Claude Code Hooks (create, update, delete, list)
argument-hint: <operation|name> [args...]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob, SlashCommand
---

# Hook Management

⚠️ **SECURITY WARNING**: Hooks execute arbitrary commands automatically. Exercise extreme caution.

Manage Claude Code Hooks with CRUD operations.

## Usage

```bash
# Show usage
/hook
/hook help

# Smart default: create if not exists, update if exists
/hook pre-commit-lint

# Explicit operations
/hook create pre-commit-lint
/hook update pre-commit-lint
/hook delete pre-commit-lint
/hook list
```

## Important: Hook Storage

Unlike Skills, Commands, and Subagents, Hooks are stored in **JSON configuration files**:
- `.claude/settings.json` (project, shared)
- `.claude/settings.local.json` (project, personal)
- `~/.claude/settings.json` (user, all projects)

This command helps manage Hooks but does not directly manipulate JSON. Instead, it delegates to hook-builder for safe configuration.

## Operation Routing

Determine operation from $1:

```bash
OPERATION="$1"
HOOK_NAME="$2"

# Parse operation
case "$OPERATION" in
  ""|help)
    echo "Usage: /hook <operation|name> [args...]"
    echo ""
    echo "Operations:"
    echo "  create <name>   - Create new Hook (guide through configuration)"
    echo "  update <name>   - Update existing Hook"
    echo "  delete <name>   - Delete Hook"
    echo "  list            - List all configured Hooks"
    echo ""
    echo "Smart default:"
    echo "  /hook <name>    - Create if not exists, update if exists"
    echo ""
    echo "⚠️  Security: All Hooks require mandatory security review"
    exit 0
    ;;
  create)
    # Explicit create
    OPERATION="create"
    HOOK_NAME="$2"
    ;;
  update)
    # Explicit update
    OPERATION="update"
    HOOK_NAME="$2"
    ;;
  delete)
    # Explicit delete
    OPERATION="delete"
    HOOK_NAME="$2"
    ;;
  list)
    # List all hooks
    OPERATION="list"
    ;;
  *)
    # Smart default: treat $1 as hook name
    HOOK_NAME="$1"

    # Check if hook exists (search in settings files)
    HOOK_EXISTS=false
    for SETTINGS_FILE in .claude/settings.json .claude/settings.local.json "$HOME/.claude/settings.json"; do
      if [ -f "$SETTINGS_FILE" ]; then
        if grep -q "\"$HOOK_NAME\"" "$SETTINGS_FILE" 2>/dev/null; then
          HOOK_EXISTS=true
          break
        fi
      fi
    done

    if [ "$HOOK_EXISTS" = true ]; then
      OPERATION="update"
    else
      OPERATION="create"
    fi
    ;;
esac
```

## Execute Operation

### CREATE Operation

⚠️ **MANDATORY SECURITY REVIEW REQUIRED**

Invoke hook-builder skill (via /ouroboros:build-hook):

```bash
if [ "$OPERATION" = "create" ]; then
  echo "Creating Hook: $HOOK_NAME"
  echo ""
  echo "⚠️  SECURITY WARNING: Hooks execute arbitrary commands automatically."
  echo "You are solely responsible for command safety and consequences."
  echo ""
fi
```

**Delegation:** Use `/ouroboros:build-hook` command which delegates to hook-builder skill:
- Step 0: Documentation verification
- Step 1: Planning and risk assessment
- Step 2: Event selection (PreToolUse, PostToolUse, etc.)
- Step 3: Matcher configuration
- Step 4: Command design with security focus
- Step 5: Complete configuration
- **Step 6: MANDATORY Security review checklist**
- Step 7: Threat modeling
- Step 8: Testing (including security tests)
- Step 9: Documentation and deployment

Pass: $HOOK_NAME

The hook-builder will:
1. Guide through configuration
2. Enforce security checklist completion
3. Generate JSON configuration
4. Assist with adding to appropriate settings file

### UPDATE Operation

Update an existing Hook configuration:

```bash
if [ "$OPERATION" = "update" ]; then
  echo "Updating Hook: $HOOK_NAME"
  echo ""
  echo "⚠️  SECURITY WARNING: Changes to Hooks require security re-review."
  echo ""

  # Find which settings file contains this hook
  for SETTINGS_FILE in .claude/settings.json .claude/settings.local.json "$HOME/.claude/settings.json"; do
    if [ -f "$SETTINGS_FILE" ]; then
      if grep -q "\"$HOOK_NAME\"" "$SETTINGS_FILE" 2>/dev/null; then
        echo "Found Hook in: $SETTINGS_FILE"
        HOOK_FILE="$SETTINGS_FILE"
        break
      fi
    fi
  done

  if [ -z "$HOOK_FILE" ]; then
    echo "Error: Hook '$HOOK_NAME' not found in any settings file"
    exit 1
  fi
fi
```

**Delegation:** Invoke hook-builder with update context:
- Read existing Hook configuration from JSON
- Ask user what to update (matcher, command, timeout, event)
- **MANDATORY: Re-run security checklist for any changes**
- Apply updates
- Re-validate and re-test

Pass: $HOOK_NAME and $HOOK_FILE

### DELETE Operation

Delete a Hook from configuration:

```bash
if [ "$OPERATION" = "delete" ]; then
  echo "Deleting Hook: $HOOK_NAME"
  echo ""

  # Find which settings file contains this hook
  HOOK_FILE=""
  for SETTINGS_FILE in .claude/settings.json .claude/settings.local.json "$HOME/.claude/settings.json"; do
    if [ -f "$SETTINGS_FILE" ]; then
      if grep -q "\"$HOOK_NAME\"" "$SETTINGS_FILE" 2>/dev/null; then
        HOOK_FILE="$SETTINGS_FILE"
        break
      fi
    fi
  done

  if [ -z "$HOOK_FILE" ]; then
    echo "Error: Hook '$HOOK_NAME' not found in any settings file"
    exit 1
  fi

  echo "Found Hook in: $HOOK_FILE"
  echo ""
  echo "⚠️  WARNING: This will remove the Hook from the settings file."
  echo ""
  read -p "Type 'yes' to confirm deletion: " CONFIRM

  if [ "$CONFIRM" = "yes" ]; then
    echo ""
    echo "To delete Hook '$HOOK_NAME' from $HOOK_FILE:"
    echo "1. Open $HOOK_FILE"
    echo "2. Locate the Hook configuration (search for '$HOOK_NAME')"
    echo "3. Remove the entire Hook object"
    echo "4. Validate JSON syntax"
    echo "5. Save the file"
    echo ""
    echo "Would you like me to help edit the file? (This requires careful JSON manipulation)"
  else
    echo "❌ Deletion cancelled"
  fi
fi
```

**Note:** Hook deletion requires careful JSON editing. The command provides guidance and can assist with Edit operations.

### LIST Operation

List all configured Hooks:

```bash
if [ "$OPERATION" = "list" ]; then
  echo "Claude Code Hooks"
  echo "================="
  echo ""

  # Project settings (shared)
  if [ -f ".claude/settings.json" ]; then
    echo "Project Hooks (.claude/settings.json):"
    if grep -q "\"hooks\"" .claude/settings.json 2>/dev/null; then
      # Parse and display hooks
      python3 -c "
import json
import sys
try:
    with open('.claude/settings.json') as f:
        data = json.load(f)
        hooks = data.get('hooks', {})
        for event_type, event_hooks in hooks.items():
            print(f'  Event: {event_type}')
            for hook_config in event_hooks:
                matcher = hook_config.get('matcher', 'N/A')
                print(f'    ✓ Matcher: {matcher}')
                for hook in hook_config.get('hooks', []):
                    hook_type = hook.get('type', 'unknown')
                    if hook_type == 'command':
                        cmd = hook.get('command', 'N/A')[:60]
                        timeout = hook.get('timeout', 'N/A')
                        print(f'      Type: {hook_type}, Timeout: {timeout}s')
                        print(f'      Command: {cmd}...')
except Exception as e:
    print(f'  Error parsing JSON: {e}')
" 2>/dev/null || echo "  No hooks configured or error parsing"
      echo ""
    else
      echo "  No hooks configured"
      echo ""
    fi
  fi

  # Project settings (local)
  if [ -f ".claude/settings.local.json" ]; then
    echo "Project Hooks - Local (.claude/settings.local.json):"
    if grep -q "\"hooks\"" .claude/settings.local.json 2>/dev/null; then
      python3 -c "
import json
try:
    with open('.claude/settings.local.json') as f:
        data = json.load(f)
        hooks = data.get('hooks', {})
        for event_type, event_hooks in hooks.items():
            print(f'  Event: {event_type}')
            for hook_config in event_hooks:
                matcher = hook_config.get('matcher', 'N/A')
                print(f'    ✓ Matcher: {matcher}')
                for hook in hook_config.get('hooks', []):
                    hook_type = hook.get('type', 'unknown')
                    if hook_type == 'command':
                        cmd = hook.get('command', 'N/A')[:60]
                        timeout = hook.get('timeout', 'N/A')
                        print(f'      Type: {hook_type}, Timeout: {timeout}s')
                        print(f'      Command: {cmd}...')
except Exception as e:
    print(f'  Error parsing JSON: {e}')
" 2>/dev/null || echo "  No hooks configured or error parsing"
      echo ""
    else
      echo "  No hooks configured"
      echo ""
    fi
  fi

  # User settings
  if [ -f "$HOME/.claude/settings.json" ]; then
    echo "User Hooks (~/.claude/settings.json):"
    if grep -q "\"hooks\"" "$HOME/.claude/settings.json" 2>/dev/null; then
      python3 -c "
import json
import sys
import os
try:
    settings_path = os.path.expanduser('~/.claude/settings.json')
    with open(settings_path) as f:
        data = json.load(f)
        hooks = data.get('hooks', {})
        for event_type, event_hooks in hooks.items():
            print(f'  Event: {event_type}')
            for hook_config in event_hooks:
                matcher = hook_config.get('matcher', 'N/A')
                print(f'    ✓ Matcher: {matcher}')
                for hook in hook_config.get('hooks', []):
                    hook_type = hook.get('type', 'unknown')
                    if hook_type == 'command':
                        cmd = hook.get('command', 'N/A')[:60]
                        timeout = hook.get('timeout', 'N/A')
                        print(f'      Type: {hook_type}, Timeout: {timeout}s')
                        print(f'      Command: {cmd}...')
except Exception as e:
    print(f'  Error parsing JSON: {e}')
" 2>/dev/null || echo "  No hooks configured or error parsing"
    else
      echo "  No hooks configured"
    fi
  fi
fi
```

## Implementation Notes

**For CREATE operation:**
- Delegate to /ouroboros:build-hook
- **MANDATORY security review checklist**
- Guide through event selection, matcher design, command construction
- Generate JSON configuration
- Assist with adding to appropriate settings file

**For UPDATE operation:**
- Read existing Hook from JSON settings file
- Ask user what to update
- **MANDATORY security re-review for any changes**
- Apply updates carefully (JSON editing)
- Re-validate and re-test

**For DELETE operation:**
- Find Hook in settings files
- Require explicit "yes" confirmation
- Provide guidance for safe JSON removal
- Optionally assist with Edit operation

**For LIST operation:**
- Parse JSON from all three settings files
- Display event type, matcher, command preview, timeout
- Group by settings file location
- Handle JSON parsing errors gracefully

## Security Reminder

⚠️ **Every Hook operation (create/update) requires:**
1. Complete security checklist
2. Proper variable quoting
3. Input validation
4. Error handling
5. Appropriate timeout
6. Testing with malicious input
7. Documented security review
