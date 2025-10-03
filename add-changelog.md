---
name: add-changelog
description: Automatically setup and maintain a comprehensive changelog following industry standards
tools: all
createdAt: "2025-10-02T21:27:54.514Z"
updatedAt: "2025-10-02T21:28:09.592Z"
---

# Add Changelog Command

Automatically setup and maintain a comprehensive changelog following industry standards.

## What This Command Does

This command will automatically:

1. **Analyze existing changelog** or create a new one following Keep a Changelog format
2. **Install automation tools** (conventional-changelog-cli)
3. **Add npm scripts** for changelog generation and releases
4. **Create release automation script** with version management
5. **Setup git commit message template** for conventional commits
6. **Generate documentation** for changelog workflow
7. **Update existing entries** based on git history

## Automated Setup Process

The command will execute these steps automatically:

### 1. Changelog Analysis & Creation

- Check for existing CHANGELOG.md
- Create or update to follow Keep a Changelog format
- Analyze git history to populate initial entries
- Add recent changes to unreleased section

### 2. Automation Tools Installation

```bash
npm install -D conventional-changelog-cli
```

### 3. NPM Scripts Addition

```json
{
  "scripts": {
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s",
    "changelog:all": "conventional-changelog -p angular -i CHANGELOG.md -s -r 0",
    "release": "node scripts/release.js",
    "release:patch": "node scripts/release.js patch",
    "release:minor": "node scripts/release.js minor",
    "release:major": "node scripts/release.js major"
  }
}
```

### 4. Release Automation Script

Creates `scripts/release.js` that:

- Updates package.json version
- Moves [Unreleased] to versioned section
- Generates conventional changelog
- Creates git commit and tag
- Builds project for release

### 5. Git Commit Template

Creates `.gitmessage` template for conventional commits:

```
# <type>: <subject>
#
# Types: feat, fix, docs, style, refactor, perf, test, chore
```

### 6. Documentation

Creates comprehensive guide at `docs/CHANGELOG_GUIDE.md`

## Usage After Setup

### Daily Development

```bash
# Add changes to [Unreleased] section manually
# Use conventional commits: git commit -m "feat: add new feature"
```

### Creating Releases

```bash
npm run release:patch   # 1.0.0 → 1.0.1
npm run release:minor   # 1.0.0 → 1.1.0
npm run release:major   # 1.0.0 → 2.0.0
```

### Manual Changelog Generation

```bash
npm run changelog       # Add recent conventional commits
npm run changelog:all   # Regenerate entire changelog
```

## Conventional Commits Format

The automation expects commits in this format:

```
type(scope): description

feat: add user authentication
fix: resolve memory leak in tasks
docs: update API documentation
style: format code with prettier
refactor: reorganize user service
test: add unit tests for auth
chore: update dependencies
```

## Features

✅ **Fully Automated Setup** - Run once, everything configured  
✅ **Git History Analysis** - Populates changelog from existing commits  
✅ **Release Automation** - One command releases with proper versioning  
✅ **Conventional Commits** - Template and automation ready  
✅ **Documentation** - Complete workflow guide included  
✅ **Integration Ready** - Works with CI/CD and GitHub releases

Perfect for maintaining professional changelogs with minimal manual work!
