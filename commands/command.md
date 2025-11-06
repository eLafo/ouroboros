---
description: Manage Claude Code Commands (create, update, delete, list)
argument-hint: <operation|name> [args...]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob, SlashCommand
---

# Command Management

Manage Claude Code Commands with CRUD operations.

## Usage

```bash
# Show usage
/command
/command help

# Smart default: create if not exists, update if exists
/command my-command-name

# With description (for create)
/command my-command "Deploy to environment"
/command "Deploy to environment"  # Infers name from description

# Explicit operations
/command create my-command
/command create my-command "Deploy to environment"
/command update my-command
/command delete my-command
/command list
```

## Operation Routing

Determine operation from arguments:

```bash
OPERATION="$1"
ARTIFACT_NAME="$2"
DESCRIPTION="$3"

# Parse operation
case "$OPERATION" in
  ""|help)
    echo "Usage: /command <operation|name> [args...]"
    echo ""
    echo "Operations:"
    echo "  create <name> [description]  - Create new Command"
    echo "  update <name>                - Update existing Command"
    echo "  delete <name>                - Delete Command"
    echo "  list                         - List all Commands"
    echo ""
    echo "Smart default:"
    echo "  /command <name>              - Create if not exists, update if exists"
    echo "  /command <name> <desc>       - Create with description"
    echo "  /command \"<desc>\"            - Create, infer name from description"
    exit 0
    ;;
  create)
    # Explicit create
    OPERATION="create"
    ARTIFACT_NAME="$2"
    DESCRIPTION="$3"
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
    # List all commands
    OPERATION="list"
    ;;
  *)
    # Smart default logic
    # If $1 contains spaces or starts with uppercase -> it's a description
    if echo "$1" | grep -q " " || echo "$1" | grep -q "^[A-Z]"; then
      OPERATION="create"
      DESCRIPTION="$1"
      ARTIFACT_NAME=""  # Will be inferred
    else
      # $1 is a name
      ARTIFACT_NAME="$1"
      DESCRIPTION="$2"

      # Check if command exists
      if [ -f ".claude/commands/$ARTIFACT_NAME.md" ] || [ -f "$HOME/.claude/commands/$ARTIFACT_NAME.md" ]; then
        OPERATION="update"
      else
        OPERATION="create"
      fi
    fi
    ;;
esac
```

## Execute Operation

### CREATE Operation

Invoke command-builder skill (via /ouroboros:build-command):

```bash
if [ "$OPERATION" = "create" ]; then
  if [ -n "$ARTIFACT_NAME" ] && [ -n "$DESCRIPTION" ]; then
    echo "Creating Command: $ARTIFACT_NAME"
    echo "Description: $DESCRIPTION"
  elif [ -n "$ARTIFACT_NAME" ]; then
    echo "Creating Command: $ARTIFACT_NAME"
  elif [ -n "$DESCRIPTION" ]; then
    echo "Creating Command from description: $DESCRIPTION"
  else
    echo "Creating new Command"
  fi
fi
```

**Delegation:** Use `/ouroboros:build-command` command which delegates to command-builder skill:
- Step 0: Documentation verification
- Step 1: Planning (gather requirements)
- Step 2: Command naming (with inference if description provided)
- Step 3: Directory setup
- Step 4: Description writing
- Step 5: Argument configuration
- Step 6: Tool restriction configuration
- Step 7: Command generation
- Step 8: Testing and validation
- Step 9: Documentation

Pass: $ARTIFACT_NAME and $DESCRIPTION (if provided)

### UPDATE Operation

Invoke command-builder with update context:

```bash
if [ "$OPERATION" = "update" ]; then
  echo "Updating Command: $ARTIFACT_NAME"

  # Find the command location
  if [ -f ".claude/commands/$ARTIFACT_NAME.md" ]; then
    COMMAND_PATH=".claude/commands/$ARTIFACT_NAME.md"
  elif [ -f "$HOME/.claude/commands/$ARTIFACT_NAME.md" ]; then
    COMMAND_PATH="$HOME/.claude/commands/$ARTIFACT_NAME.md"
  else
    echo "Error: Command '$ARTIFACT_NAME' not found"
    exit 1
  fi

  echo "Found at: $COMMAND_PATH"
fi
```

**Delegation:** Invoke command-builder with update context:
- Read existing Command file
- Ask user what to update (description, arguments, prompt, tools, etc.)
- Apply updates
- Re-validate
- Re-test

Pass: $ARTIFACT_NAME and $COMMAND_PATH

### DELETE Operation

Delete a Command with confirmation:

```bash
if [ "$OPERATION" = "delete" ]; then
  echo "Deleting Command: $ARTIFACT_NAME"

  # Find the command location
  if [ -f ".claude/commands/$ARTIFACT_NAME.md" ]; then
    COMMAND_FILE=".claude/commands/$ARTIFACT_NAME.md"
  elif [ -f "$HOME/.claude/commands/$ARTIFACT_NAME.md" ]; then
    COMMAND_FILE="$HOME/.claude/commands/$ARTIFACT_NAME.md"
  else
    echo "Error: Command '$ARTIFACT_NAME' not found"
    exit 1
  fi

  echo "Found at: $COMMAND_FILE"
  echo ""
  echo "⚠️  WARNING: This will permanently delete the Command."
  echo ""
  read -p "Type 'yes' to confirm deletion: " CONFIRM

  if [ "$CONFIRM" = "yes" ]; then
    rm "$COMMAND_FILE"
    echo "✅ Deleted: $COMMAND_FILE"
  else
    echo "❌ Deletion cancelled"
  fi
fi
```

### LIST Operation

List all Commands:

```bash
if [ "$OPERATION" = "list" ]; then
  echo "Claude Code Commands"
  echo "===================="
  echo ""

  # Project commands
  if [ -d ".claude/commands" ]; then
    echo "Project Commands (.claude/commands/):"
    find .claude/commands -name "*.md" -exec sh -c '
      COMMAND_NAME=$(basename {} .md)
      DESCRIPTION=$(grep "^description:" {} | head -1 | cut -d: -f2- | xargs)
      echo "  ✓ $COMMAND_NAME"
      echo "    $DESCRIPTION"
    ' \;
    echo ""
  fi

  # User commands
  if [ -d "$HOME/.claude/commands" ]; then
    echo "User Commands (~/.claude/commands/):"
    find "$HOME/.claude/commands" -name "*.md" -exec sh -c '
      COMMAND_NAME=$(basename {} .md)
      DESCRIPTION=$(grep "^description:" {} | head -1 | cut -d: -f2- | xargs)
      echo "  ✓ $COMMAND_NAME"
      echo "    $DESCRIPTION"
    ' \;
  fi
fi
```

## Implementation Notes

**For CREATE operation:**
- Delegate to /ouroboros:build-command
- Preserve existing argument handling (name, description, inference)
- Support all existing patterns from build-command

**For UPDATE operation:**
- Read existing Command file
- Ask user what to update
- Re-validate after changes
- Test invocation

**For DELETE operation:**
- Require explicit "yes" confirmation
- Remove command file
- Clear feedback

**For LIST operation:**
- Search project and user command directories
- Display name and description
- Group by location
