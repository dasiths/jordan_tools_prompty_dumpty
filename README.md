# JK Tools Commands - External Repository Example

An example PromptyDumpty package demonstrating the **external repository reference** feature.

**Author:** Jordan Knight  
**License:** MIT  
**Version:** 1.0.0

## What This Example Demonstrates

This package showcases how to use PromptyDumpty's `external_repository` feature to create a **wrapper package** that references files from another Git repository without forking or copying code.

### The Problem It Solves

You want to:
- ✅ Package and distribute prompts/commands from a repository you don't own
- ✅ Keep the source files in their original repository
- ✅ Version your package independently from the source repository
- ✅ Avoid forking or duplicating content
- ✅ Curate a subset of files for your team

### The Solution: External Repository Reference

Instead of copying files into your package repository, you **reference** them from the external source repository using a specific commit hash.

## How It Works

### Repository Structure

**Wrapper Package Repository** (this repo): `https://github.com/dasiths/jordan_tools_prompty_dumpty`
- Contains **only** the `dumpty.package.yaml` manifest
- No actual prompt/command files
- Declares what to install and from where

**Source Repository** (external): `https://github.com/jakkaj/tools`
- Contains the actual prompt/command markdown files
- Owned and maintained by Jordan Knight
- Files located in `agents/commands/` directory

### The Manifest

```yaml
name: jk-tools-commands
version: 1.0.0
description: Comprehensive workflow commands for structured development
manifest_version: 1.0
author: Jordan Knight
license: MIT
homepage: https://github.com/dasiths/jordans_tools

# This is the key feature - reference files from external repo
external_repository: https://github.com/jakkaj/tools@f64d36011a2d8acd356bacafe61383ef96ac29fc

agents:
  copilot:
    prompts:
      - name: plan-1-specify
        description: Create detailed feature specification
        file: agents/commands/plan-1-specify.md  # ← File from external repo
        installed_path: plan-1-specify.prompt.md
      # ... more prompts
```

### What Happens During Installation

When you run `dumpty install https://github.com/dasiths/jordan_tools_prompty_dumpty`:

1. **PromptyDumpty clones TWO repositories:**
   - Manifest repo: `dasiths/jordan_tools_prompty_dumpty` (for the manifest)
   - External repo: `jakkaj/tools` at commit `f64d360...` (for the files)

2. **Resolves file paths:**
   - All `file:` paths in the manifest resolve to the external repository
   - Example: `agents/commands/plan-1-specify.md` → found in `jakkaj/tools` repo

3. **Installs files to correct locations:**
   - For Copilot: `.github/prompts/jk-tools-commands/plan-1-specify.prompt.md`
   - For Claude: `.claude/commands/jk-tools-commands/plan-1-specify.md`

4. **Tracks both repos in lockfile:**
   ```yaml
   external_repository:
     source: https://github.com/jakkaj/tools
     commit: f64d36011a2d8acd356bacafe61383ef96ac29fc
   ```

## Key Concepts

### Commit Hash Pinning

```yaml
external_repository: https://github.com/jakkaj/tools@f64d36011a2d8acd356bacafe61383ef96ac29fc
```

- **Must use full 40-character commit hash** (not tags or branches)
- Provides **immutable reference** - files won't change unexpectedly
- Get current commit: `git rev-parse HEAD` in the external repository

### Path Resolution

When `external_repository` is specified:
- ✅ **ALL file paths resolve from the external repository**
- ❌ No files should exist in the manifest repository
- ⚠️ Warning shown if manifest repo contains unexpected files

### Dual Repository Tracking

The lockfile tracks both repositories:
```yaml
packages:
  - name: jk-tools-commands
    version: 1.0.0
    source: https://github.com/dasiths/jordan_tools_prompty_dumpty
    resolved: <manifest-repo-commit>
    external_repository:
      source: https://github.com/jakkaj/tools
      commit: f64d36011a2d8acd356bacafe61383ef96ac29fc
```

## Installation

```bash
dumpty install https://github.com/dasiths/jordan_tools_prompty_dumpty
```

## What Gets Installed

This package provides **16 workflow commands** from Jordan Knight's tools:

### Planning Workflow

Complete feature development lifecycle:
- `plan-1-specify` - Feature specification
- `plan-2-file-discover` - Identify affected files
- `plan-3-feature-make` - Implementation
- `plan-4-iterate` - Refinement
- `plan-5-validation-strategy` - Test planning
- `plan-6-write-tests` - Test creation
- `plan-7-finalize` - Final review
- `plan-8-commit` - Commit message generation

### Setup Commands

New codebase orientation:
- `setup-1-new-codebase-understand` - Codebase analysis
- `setup-2-build-and-run` - Environment setup
- `setup-3-write-readme` - Documentation creation

### Other Commands

- `get-context-size-estimate` - Token usage estimation
- `research-find-solution` - Solution discovery
- `research-web` - Web research
- `test-create` - Test generation
- `info-show-command-list` - Command listing

## Creating Your Own Wrapper Package

### Step 1: Find Content to Package

Identify a repository with files you want to package:
```bash
# Example: You want to package prompts from someone else's repo
git clone https://github.com/jakkaj/tools
cd tools
git rev-parse HEAD  # Get commit hash: f64d36011a2d8acd356bacafe61383ef96ac29fc
```

### Step 2: Create Manifest Repository

Create a **new repository** with **only** a `dumpty.package.yaml`:

```yaml
name: my-wrapper-package
version: 1.0.0
description: Curated collection of AI commands
manifest_version: 1.0
author: Your Name
license: MIT

# Reference external repository at specific commit
external_repository: https://github.com/jakkaj/tools@f64d36011a2d8acd356bacafe61383ef96ac29fc

agents:
  copilot:
    prompts:
      - name: my-command
        description: Command description
        file: agents/commands/plan-1-specify.md  # Path in external repo
        installed_path: my-command.prompt.md
```

### Step 3: Verify File Paths

All `file:` paths must exist in the **external repository**, not your manifest repo:

```bash
# Clone the external repo and verify paths
git clone https://github.com/jakkaj/tools
cd tools
git checkout f64d36011a2d8acd356bacafe61383ef96ac29fc
ls agents/commands/plan-1-specify.md  # ✅ File exists
```

### Step 4: Test Installation

```bash
dumpty install https://github.com/yourusername/my-wrapper-package
```

### Step 5: Distribute

Share your manifest repository URL - users install from your repo, files come from the external source.

## Benefits of This Approach

✅ **No Forking Required**
- Package content you don't own without forking
- Original author retains full control

✅ **Independent Versioning**
- Your package version (`1.0.0`) is independent from source repo
- Pin to specific source commit for stability

✅ **Content Curation**
- Select specific files from larger repositories
- Create themed packages from multiple sources

✅ **No Duplication**
- No need to copy/maintain duplicate files
- Reduces maintenance burden

✅ **Proper Attribution**
- Original repository remains the source of truth
- Author field and repository URLs maintain credit

## Trade-offs

⚠️ **Immutable References**
- Must update commit hash to get source changes
- Can't use branches or tags (only commit hashes)

⚠️ **Two Repository Dependencies**
- Both manifest and external repos must remain accessible
- External repo deletion breaks installation

⚠️ **Version Management**
- Your version number independent from source
- Need to track source changes manually

## Support

- **This Package**: https://github.com/dasiths/jordan_tools_prompty_dumpty
- **Source Content**: https://github.com/jakkaj/tools
- **PromptyDumpty Docs**: https://promptydumpty.dev

### 🔧 Utility Commands (1 command)

- **util-0-handover** - Generate handover documentation for context transfer

### 🔍 Research Commands (2 commands)

- **deepresearch** - Perform deep research with web search
- **substrateresearch** - Research using Substrate framework

### 🧪 Testing Commands (1 command)

- **tad** - Test-After-Development workflow

### 💡 Info Commands (1 command)

- **didyouknow** - Share VS Code tips and tricks

Plus a comprehensive **commands-readme** guide to help you navigate the entire workflow.

## Supported AI Assistants

- ✅ **GitHub Copilot** - Installed as prompts in `.github/prompts/jk-tools-commands/`
- ✅ **Claude** - Installed as commands in `.claude/commands/jk-tools-commands/`

## The /plan Workflow

This package implements a structured development workflow designed to help teams move from idea to implementation systematically:

```
┌─────────────────────────────────────────────────────┐
│  Phase 0: Constitution & Norms                      │
│  Establish project guidelines and standards         │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  Phase 1-2: Specification & Clarification           │
│  Define requirements and resolve ambiguities        │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  Phase 3: Architecture Design                       │
│  Design system architecture + ADRs                  │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  Phase 4-5: Planning & Task Breakdown               │
│  Create implementation plan and task hierarchy      │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  Phase 6: Implementation & Progress Tracking        │
│  Execute phases and track progress                  │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  Phase 7: Code Review                               │
│  Comprehensive review before completion             │
└─────────────────────────────────────────────────────┘
```

## Usage Examples

### GitHub Copilot

In VS Code with GitHub Copilot, use the `@workspace` mention with `/` commands:

```
@workspace /plan-1-specify
```

Or select from the prompt picker:
1. Press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
2. Type "Chat: Open Prompt Library"
3. Search for "plan-" to see all planning commands

### Claude

In projects using Claude Desktop or Claude Code:

```
/plan-1-specify
```

All commands are available in your commands directory.

## Key Features

- **Structured Workflow** - Follow a proven development process from planning to delivery
- **Documentation-Driven** - Generate architecture decisions, specifications, and handover docs
- **Progress Tracking** - Built-in commands to track implementation progress
- **Flexibility** - Use individual commands or follow the complete workflow
- **Multi-Agent Support** - Works with both GitHub Copilot and Claude

## Example Workflow

Here's how you might use these commands for a new feature:

1. **Start with Constitution** - `plan-0-constitution` to align on project standards
2. **Specify the Feature** - `plan-1-specify` to document requirements
3. **Clarify Details** - `plan-2-clarify` to resolve ambiguities
4. **Design Architecture** - `plan-3-architect` to plan the solution
5. **Document Decisions** - `plan-3a-adr` for key architectural choices
6. **Create Implementation Plan** - `plan-4-complete-the-plan` 
7. **Break Into Tasks** - `plan-5-phase-tasks-and-brief` for task breakdown
8. **Implement** - `plan-6-implement-phase` to execute
9. **Track Progress** - `plan-6a-update-progress` to monitor completion
10. **Review Code** - `plan-7-code-review` before merging

## Source Repository

This package references files from Jordan Knight's tools repository:
- **Source:** https://github.com/jakkaj/tools
- **Commit:** `f64d36011a2d8acd356bacafe61383ef96ac29fc`

The package uses PromptyDumpty's external repository feature to pull the latest content without forking.

## Updates

To update to the latest version:

```bash
dumpty update jk-tools-commands
```

## Uninstall

```bash
dumpty uninstall jk-tools-commands
```

## Contributing

This is a wrapper package. For improvements to the actual commands and workflows:
- Visit the source repository: https://github.com/jakkaj/tools
- Original repository: https://github.com/dasiths/jordans_tools

## License

MIT License

## Links

- **Package Repository:** https://github.com/dasiths/jordan_tools_prompty_dumpty
- **Source Repository:** https://github.com/jakkaj/tools
- **PromptyDumpty:** https://dumpty.dev

---

*Need help with the workflow? Start with the `commands-readme` prompt for a complete guide!*
