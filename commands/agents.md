---
description: Manage Claude Code Subagents (create, update, delete, list)
argument-hint: <operation|name> [args...]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob, SlashCommand
---

# Subagent Management

Manage Claude Code Subagents with CRUD operations.

## Usage

```bash
# Show usage
/agents
/agents help

# Smart default: create if not exists, update if exists
/agents code-reviewer

# Explicit operations
/agents create code-reviewer
/agents update code-reviewer
/agents delete code-reviewer
/agents list
```

## Operation Routing

Determine operation from $1:

```bash
OPERATION="$1"
ARTIFACT_NAME="$2"

# Parse operation
case "$OPERATION" in
  ""|help)
    echo "Usage: /agents <operation|name> [args...]"
    echo ""
    echo "Operations:"
    echo "  create <name>   - Create new Subagent"
    echo "  update <name>   - Update existing Subagent"
    echo "  delete <name>   - Delete Subagent"
    echo "  list            - List all Subagents"
    echo ""
    echo "Smart default:"
    echo "  /agents <name>  - Create if not exists, update if exists"
    exit 0
    ;;
  create)
    # Explicit create
    OPERATION="create"
    ARTIFACT_NAME="$2"
    ;;
  update)
    # Explicit update
    OPERATION="update"
    ARTIFACT_NAME="$2"
    ;;
  delete)
    # Explicit delete
    OPERATION="delete"
    ARTIFACT_NAME="$2"
    ;;
  list)
    # List all subagents
    OPERATION="list"
    ;;
  *)
    # Smart default: treat $1 as artifact name
    ARTIFACT_NAME="$1"
    # Check if subagent exists
    if [ -f ".claude/agents/$ARTIFACT_NAME.md" ] || [ -f "$HOME/.claude/agents/$ARTIFACT_NAME.md" ]; then
      OPERATION="update"
    else
      OPERATION="create"
    fi
    ;;
esac
```

## Execute Operation

### CREATE Operation

Invoke subagent-builder skill (via /ouroboros:build-subagent):

```bash
if [ "$OPERATION" = "create" ]; then
  echo "Creating Subagent: $ARTIFACT_NAME"
fi
```

**Delegation:** Use `/ouroboros:build-subagent` command which delegates to subagent-builder skill:
- Step 0: Documentation verification
- Step 1: Planning (identify purpose, tools, complexity)
- Step 2: Naming and scope
- Step 3: Description crafting
- Step 4: Tool access configuration
- Step 5: Model selection
- Step 6: System prompt design
- Step 7: Complete configuration and testing

Pass: $ARTIFACT_NAME

### UPDATE Operation

Invoke subagent-builder with update context:

```bash
if [ "$OPERATION" = "update" ]; then
  echo "Updating Subagent: $ARTIFACT_NAME"

  # Find the subagent location
  if [ -f ".claude/agents/$ARTIFACT_NAME.md" ]; then
    SUBAGENT_PATH=".claude/agents/$ARTIFACT_NAME.md"
  elif [ -f "$HOME/.claude/agents/$ARTIFACT_NAME.md" ]; then
    SUBAGENT_PATH="$HOME/.claude/agents/$ARTIFACT_NAME.md"
  else
    echo "Error: Subagent '$ARTIFACT_NAME' not found"
    exit 1
  fi

  echo "Found at: $SUBAGENT_PATH"
fi
```

**Delegation:** Invoke subagent-builder with update context:
- Read existing Subagent configuration
- Ask user what to update (description, tools, model, system prompt)
- Apply updates
- Re-validate
- Re-test delegation

Pass: $ARTIFACT_NAME and $SUBAGENT_PATH

### DELETE Operation

Delete a Subagent with confirmation:

```bash
if [ "$OPERATION" = "delete" ]; then
  echo "Deleting Subagent: $ARTIFACT_NAME"

  # Find the subagent location
  if [ -f ".claude/agents/$ARTIFACT_NAME.md" ]; then
    SUBAGENT_FILE=".claude/agents/$ARTIFACT_NAME.md"
  elif [ -f "$HOME/.claude/agents/$ARTIFACT_NAME.md" ]; then
    SUBAGENT_FILE="$HOME/.claude/agents/$ARTIFACT_NAME.md"
  else
    echo "Error: Subagent '$ARTIFACT_NAME' not found"
    exit 1
  fi

  echo "Found at: $SUBAGENT_FILE"
  echo ""
  echo "⚠️  WARNING: This will permanently delete the Subagent."
  echo ""
  read -p "Type 'yes' to confirm deletion: " CONFIRM

  if [ "$CONFIRM" = "yes" ]; then
    rm "$SUBAGENT_FILE"
    echo "✅ Deleted: $SUBAGENT_FILE"
  else
    echo "❌ Deletion cancelled"
  fi
fi
```

### LIST Operation

List all Subagents:

```bash
if [ "$OPERATION" = "list" ]; then
  echo "Claude Code Subagents"
  echo "====================="
  echo ""

  # Project subagents
  if [ -d ".claude/agents" ]; then
    echo "Project Subagents (.claude/agents/):"
    find .claude/agents -name "*.md" -exec sh -c '
      SUBAGENT_NAME=$(basename {} .md)
      DESCRIPTION=$(grep "^description:" {} | head -1 | cut -d: -f2- | xargs)
      MODEL=$(grep "^model:" {} | head -1 | cut -d: -f2- | xargs)
      echo "  ✓ $SUBAGENT_NAME [$MODEL]"
      echo "    $DESCRIPTION"
    ' \;
    echo ""
  fi

  # User subagents
  if [ -d "$HOME/.claude/agents" ]; then
    echo "User Subagents (~/.claude/agents/):"
    find "$HOME/.claude/agents" -name "*.md" -exec sh -c '
      SUBAGENT_NAME=$(basename {} .md)
      DESCRIPTION=$(grep "^description:" {} | head -1 | cut -d: -f2- | xargs)
      MODEL=$(grep "^model:" {} | head -1 | cut -d: -f2- | xargs)
      echo "  ✓ $SUBAGENT_NAME [$MODEL]"
      echo "    $DESCRIPTION"
    ' \;
  fi
fi
```

## Implementation Notes

**For CREATE operation:**
- Delegate to /ouroboros:build-subagent
- Preserve all existing build-subagent logic
- Guide through tool selection, model choice, system prompt design

**For UPDATE operation:**
- Read existing Subagent configuration (YAML + system prompt)
- Ask user what to update:
  - Description (affects delegation decisions)
  - Tools (expand or restrict access)
  - Model (change capability level)
  - System prompt (refine instructions)
- Re-validate after changes
- Test delegation

**For DELETE operation:**
- Require explicit "yes" confirmation
- Remove subagent file
- Clear feedback

**For LIST operation:**
- Search project and user agent directories
- Display name, model, and description
- Group by location
- Show model in brackets for quick reference
