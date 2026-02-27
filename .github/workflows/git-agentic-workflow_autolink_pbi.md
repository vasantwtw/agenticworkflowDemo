---
description: |
  This workflow automatically links Product Backlog Items (PBIs) to Pull Requests
  when the PR branch follows naming conventions like feature/405604-Description or
  bug/strategic-2/405604-Description. It extracts the PBI number and creates the link.

on:
  pull_request:
    types:
      - opened
      - synchronize
      - reopened

permissions:
  contents: read
  issues: read
  models: read
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

Automatically link Azure DevOps Product Backlog Items (PBIs) to Pull Requests based on branch naming conventions.

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

4. **Link the PBI to the PR** using Azure DevOps API
   - Retrieve the work item by PBI number
   - Create an artifact link between the work item and this pull request
   - Handle errors gracefully if PBI doesn't exist

5. **Post a comment** on the pull request
   - Success: `✅ Successfully linked Work Item #[PBI-NUMBER] to this Pull Request`
   - Not found: `⚠️ Work Item #[PBI-NUMBER] not found. Please verify the PBI number.`
   - Error: `❌ Unable to link Work Item. Please check permissions.`

## Expected Behavior

- ✅ Auto-link when branch follows naming convention
- ✅ Provide feedback via PR comments
- ✅ Handle errors gracefully
- ✅ Skip non-matching branches silently

