---
description: |
  This workflow automatically links Product Backlog Items (PBIs) to Pull Requests
  when the PR branch follows naming conventions like feature/405604-Description or
  bug/strategic-2/405604-Description. It extracts the PBI number and creates the link
  using GitHub's Azure Boards integration (AB#).

on:
  pull_request:
    types:
      - opened
      - synchronize
      - reopened

permissions:
  contents: read
  issues: read
  pull-requests: read

network: defaults

tools:
  github:
    lockdown: false

safe-outputs:
  mentions: false
  allowed-github-references: []
  add-comment:
    pull-requests: true

engine: copilot
---

# Auto-Link PBI to Pull Request

Automatically link Azure DevOps Product Backlog Items (PBIs) to Pull Requests based on branch naming conventions using GitHub's Azure Boards integration.

## Branch Naming Patterns

The workflow recognizes these branch naming patterns:

1. `feature/{PBI-NUMBER}-{Description}`
   - Example: `feature/405604-AgenticWorkFlowToLinkPBI`

2. `bug/{PBI-NUMBER}-{Description}`
   - Example: `bug/405604-AgenticWorkFlowToLinkPBI`

3. `feature/strategic-2/{PBI-NUMBER}-{Description}`
   - Example: `feature/strategic-2/405604-AgenticWorkFlowToLinkPBI`

4. `bug/strategic-2/{PBI-NUMBER}-{Description}`
   - Example: `bug/strategic-2/405604-AgenticWorkFlowToLinkPBI`

**Pattern Regex:** `^(feature|bug)(?:/strategic-2)?/(\d+)-(.+)$`

## Instructions

When this workflow is triggered by a pull request event:

1. **Get the branch name** from the pull request's source branch

2. **Check if the branch matches** any of the supported patterns above
   - If no match, exit gracefully (no action needed)

3. **Extract the PBI number** from the branch name (the numeric portion)
   - Example: From `feature/405604-AgenticWorkFlowToLinkPBI`, extract `405604`

4. **Post a comment with AB# syntax** to trigger GitHub's Azure Boards integration
   - Comment format: `AB#{PBI-NUMBER}`
   - GitHub will automatically create the link to Azure DevOps when Azure Boards app is installed
   - Example comment: `AB#405604`

5. **Include success message** in the same comment
   - Format: `✅ Linked Azure DevOps Work Item AB#{PBI-NUMBER} to this Pull Request`
   - GitHub's Azure Boards integration will convert `AB#405604` into a clickable link
   - If comment fails: `❌ Unable to post comment. Please check permissions.`

## Expected Behavior

- ✅ Auto-link when branch follows naming convention using AB# syntax
- ✅ Post single comment with AB# reference
- ✅ GitHub Azure Boards app converts AB# to clickable link automatically
- ✅ Handle comment posting errors gracefully
- ✅ Skip non-matching branches silently

## Prerequisites

- **GitHub Azure Boards App**: Must be installed and configured for the repository
- **Permissions**: Workflow requires `pull-requests: write` permission (already configured)
- **No ADO Credentials Needed**: GitHub's native integration handles the linking - no tokens or API credentials required

