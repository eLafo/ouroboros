---
description: Manage Claude Code Skills (create, update, delete, list)
argument-hint: <operation|name> [args...]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob, Skill
---

# Skill Management Command

Manage Claude Code Skills with CRUD operations.

## Usage

```bash
# Show usage
/skill
/skill help

# Smart default: create if not exists, update if exists
/skill my-skill-name

# Explicit operations
/skill create my-skill-name
/skill update my-skill-name
/skill delete my-skill-name
/skill list
```

## Operation Routing

Determine operation from $1:

```bash
OPERATION="$1"
ARTIFACT_NAME="$2"

# Parse operation
case "$OPERATION" in
  ""|help)
    echo "Usage: /skill <operation|name> [args...]"
    echo ""
    echo "Operations:"
    echo "  create <name>   - Create new Skill"
    echo "  update <name>   - Update existing Skill"
    echo "  delete <name>   - Delete Skill"
    echo "  list            - List all Skills"
    echo ""
    echo "Smart default:"
    echo "  /skill <name>   - Create if not exists, update if exists"
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
    # List all skills
    OPERATION="list"
    ;;
  *)
    # Smart default: treat $1 as artifact name
    ARTIFACT_NAME="$1"
    # Check if skill exists
    if [ -f ".claude/skills/$ARTIFACT_NAME/SKILL.md" ] || [ -f "$HOME/.claude/skills/$ARTIFACT_NAME/SKILL.md" ]; then
      OPERATION="update"
    else
      OPERATION="create"
    fi
    ;;
esac
```

## Execute Operation

Based on determined operation:

### CREATE Operation

Invoke the skill-builder skill to create a new Skill:

```bash
if [ "$OPERATION" = "create" ]; then
  echo "Creating Skill: $ARTIFACT_NAME"
  # Delegate to skill-builder skill
  # The skill-builder will guide through the creation workflow
fi
```

**Delegation:** Use the `skill-builder` skill to guide the user through creating a new Skill with:
- Step 0: Documentation verification
- Step 1: Planning and requirements gathering
- Step 2: Description crafting
- Step 3: Directory setup
- Step 4: YAML frontmatter generation
- Step 5: Content development
- Step 6: Validation
- Step 7: Activation testing
- Step 8: Documentation

Pass the artifact name: $ARTIFACT_NAME

### UPDATE Operation

Invoke the skill-builder skill to update an existing Skill:

```bash
if [ "$OPERATION" = "update" ]; then
  echo "Updating Skill: $ARTIFACT_NAME"
  # Find the skill location
  if [ -f ".claude/skills/$ARTIFACT_NAME/SKILL.md" ]; then
    SKILL_PATH=".claude/skills/$ARTIFACT_NAME/SKILL.md"
  elif [ -f "$HOME/.claude/skills/$ARTIFACT_NAME/SKILL.md" ]; then
    SKILL_PATH="$HOME/.claude/skills/$ARTIFACT_NAME/SKILL.md"
  else
    echo "Error: Skill '$ARTIFACT_NAME' not found"
    exit 1
  fi

  echo "Found at: $SKILL_PATH"
  # Delegate to skill-builder skill for update
fi
```

**Delegation:** Use the `skill-builder` skill with update context:
- Read existing Skill
- Ask user what to update (description, content, tools, etc.)
- Apply updates
- Re-validate
- Re-test activation

Pass the artifact name and path: $ARTIFACT_NAME at $SKILL_PATH

### DELETE Operation

Delete a Skill with confirmation:

```bash
if [ "$OPERATION" = "delete" ]; then
  echo "Deleting Skill: $ARTIFACT_NAME"

  # Find the skill location
  if [ -d ".claude/skills/$ARTIFACT_NAME" ]; then
    SKILL_DIR=".claude/skills/$ARTIFACT_NAME"
  elif [ -d "$HOME/.claude/skills/$ARTIFACT_NAME" ]; then
    SKILL_DIR="$HOME/.claude/skills/$ARTIFACT_NAME"
  else
    echo "Error: Skill '$ARTIFACT_NAME' not found"
    exit 1
  fi

  echo "Found at: $SKILL_DIR"
  echo ""
  echo "⚠️  WARNING: This will permanently delete the Skill and all its files."
  echo ""
  # Ask user to confirm
  read -p "Type 'yes' to confirm deletion: " CONFIRM

  if [ "$CONFIRM" = "yes" ]; then
    rm -rf "$SKILL_DIR"
    echo "✅ Deleted: $SKILL_DIR"
  else
    echo "❌ Deletion cancelled"
  fi
fi
```

### LIST Operation

List all Skills:

```bash
if [ "$OPERATION" = "list" ]; then
  echo "Claude Code Skills"
  echo "=================="
  echo ""

  # Project skills
  if [ -d ".claude/skills" ]; then
    echo "Project Skills (.claude/skills/):"
    find .claude/skills -name "SKILL.md" -exec sh -c '
      SKILL_DIR=$(dirname {})
      SKILL_NAME=$(basename "$SKILL_DIR")
      DESCRIPTION=$(grep "^description:" {} | head -1 | cut -d: -f2- | xargs)
      echo "  ✓ $SKILL_NAME"
      echo "    $DESCRIPTION"
    ' \;
    echo ""
  fi

  # User skills
  if [ -d "$HOME/.claude/skills" ]; then
    echo "User Skills (~/.claude/skills/):"
    find "$HOME/.claude/skills" -name "SKILL.md" -exec sh -c '
      SKILL_DIR=$(dirname {})
      SKILL_NAME=$(basename "$SKILL_DIR")
      DESCRIPTION=$(grep "^description:" {} | head -1 | cut -d: -f2- | xargs)
      echo "  ✓ $SKILL_NAME"
      echo "    $DESCRIPTION"
    ' \;
  fi
fi
```

## Implementation Notes

**For CREATE operation:**
- Invoke skill-builder skill
- Pass artifact name as argument
- Skill-builder handles entire creation workflow
- This preserves all existing build-skill logic

**For UPDATE operation:**
- Read existing Skill file
- Invoke skill-builder with update context
- Allow user to modify specific sections
- Re-validate after changes

**For DELETE operation:**
- Require explicit "yes" confirmation
- Remove entire skill directory
- Provide clear feedback

**For LIST operation:**
- Search both project and user skill directories
- Display name and description for each
- Group by location (project vs user)
