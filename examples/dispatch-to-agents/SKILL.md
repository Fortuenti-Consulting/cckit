---
name: dispatch-to-agents
description: "Create Paperclip issue and dispatch work to the right agent (avoids autocomplete)"
license: MIT
metadata:
  author: Ricardo Iguarán
  version: "1.0"
  keywords: "paperclip, issue, dispatch, agent, workflow"
---

# Dispatch to Agents Skill

This skill replaces the problematic `/dispatch` command which gets autocompleted to `/dispatching-parallel-agents`. Use this skill to create structured issues in Paperclip and dispatch them to the appropriate agent.

## How to Use

**Basic usage:**
```
/dispatch-to-agents "Your issue title"
```

**With priority:**
```
/dispatch-to-agents "Your issue title" high
```

**With description:**
```
/dispatch-to-agents "Issue title" high "Detailed description of what needs to be done"
```

## What This Skill Does

1. **Gathers input** from you:
   - **Title** (required): What is the issue about?
   - **Priority** (optional): high, medium, low, or critical (default: medium)
   - **Description** (optional): More details about what needs to be done

2. **Classifies the agent** based on keywords in your title and description:
   - **Designer** ← if you mention: frontend, design, CSS, HTML, UI, layout, responsive, visual, button, component, styling, interface, landing page, color, icon
   - **Engineer** ← if you mention: backend, API, database, FastAPI, PostgreSQL, code, function, service, endpoint, query, algorithm, performance, optimization, schema, migration, auth
   - **DevOps** ← if you mention: deploy, VPS, nginx, Docker, infrastructure, SSL, DNS, build, devops, server, systemd, container, kubernetes, cloud, monitoring, CI/CD
   - **QA** ← if you mention: testing, test, QA, verification, audit, bug, reproduce, acceptance, coverage, manual, automated, E2E
   - **CEO** ← if you mention: strategy, hiring, planning, business, product, roadmap, multi-discipline, unclear scope, decision, leadership, vision

3. **Provides Claude Code with a structured request** to:
   - Create a Paperclip issue with the title, description, and priority
   - Assign it to the classified agent
   - Wake up the agent immediately
   - Report back the issue number (FOR-XX) and Paperclip URL

4. **Claude Code then executes** the actual API calls to:
   - POST to `/api/companies/{companyId}/issues` to create the issue
   - POST to `/api/agents/{agentId}/wakeup` to wake the agent
   - Return the issue identifier and confirmation

## Example Conversations

### Example 1: Simple Feature Request
```
User: /dispatch-to-agents "Add dark mode toggle to portal"
Skill: Analyzing... (Keywords: "dark mode", "toggle", "portal" = UI)
Skill: Classified as: Designer Agent
Skill: Providing Claude Code with request...
Claude Code: Creating issue FOR-42 and waking Designer...
Result: ✅ Designer has been assigned FOR-42 to implement dark mode toggle
```

### Example 2: Backend Issue with Priority
```
User: /dispatch-to-agents "Fix authentication token expiration bug" critical "Users are getting logged out after 1 hour. API auth endpoint is rejecting valid tokens."
Skill: Analyzing... (Keywords: "authentication", "token", "API", "bug" = Backend)
Skill: Classified as: Engineer Agent
Skill: Priority: critical
Skill: Providing Claude Code with request...
Claude Code: Creating issue FOR-43 and waking Engineer...
Result: ✅ Engineer has been assigned critical issue FOR-43 for token expiration fix
```

### Example 3: DevOps Infrastructure
```
User: /dispatch-to-agents "Update SSL certificates on production VPS" high
Skill: Analyzing... (Keywords: "SSL", "VPS", "production" = Infrastructure)
Skill: Classified as: DevOps Agent
Skill: Providing Claude Code with request...
Claude Code: Creating issue FOR-44 and waking DevOps...
Result: ✅ DevOps has been assigned FOR-44 to update SSL certificates
```

## Important Notes

### This Skill is Update-Proof

This skill is stored in `~/.claude/skills/dispatch-to-agents/SKILL.md` which:
- ✅ Survives Claude Code updates (stored in user home, not Claude Code binary)
- ✅ Auto-indexes in Claude Code (appears in "/" menu without configuration)
- ✅ Updates automatically when you edit this file
- ✅ Works with any Claude Code version

### Why This Naming?

- **Old approach:** `/dispatch` → Claude Code autocompletes to `/dispatching-parallel-agents`
- **New approach:** `/dispatch-to-agents` → Explicit, no autocomplete conflicts, clear intent
- **Benefit:** Access via "/" menu is unambiguous

### Agent Classification

The classification uses keyword matching on your title and description. If multiple agent types match, the skill picks the most relevant one. If you disagree with the classification:
- You can edit the Paperclip issue assignment manually after creation, OR
- Use the title/description to be more specific about the work type

### Structured Prompt Approach

This skill works by:
1. **You provide:** Title, priority, description
2. **Skill provides:** Structured request to Claude Code describing what APIs to call
3. **Claude Code executes:** The actual Paperclip API calls and agent wakeup
4. **You receive:** Confirmation with issue ID and URL

This separation of concerns means:
- ✅ No API keys stored in the skill
- ✅ Claude Code handles all API interactions safely
- ✅ Easy to audit and modify
- ✅ Reusable pattern for other skills

## Common Issues

**Q: How do I know if the issue was created?**
A: Claude Code will report back with the issue identifier (FOR-XX) and a URL to view it in Paperclip.

**Q: The agent isn't the one I wanted**
A: The classification is keyword-based. Either:
   - Use more specific keywords in your title/description, OR
   - Create the issue and manually reassign it in Paperclip, OR
   - Give feedback to improve the classification rules

**Q: Can I use this for non-Fortuenti projects?**
A: Yes! The skill dispatches to your Fortuenti Paperclip instance. The same pattern can be adapted for other systems.

**Q: Is this secure?**
A: Yes. The skill only gathers input and provides a structured request to Claude Code. All actual API calls are handled by Claude Code, and no sensitive data is stored in the skill file.

## How This Skill is Made

This skill is written as a Markdown document with YAML frontmatter. When you invoke it:
1. Claude Code loads this file
2. Claude Code parses the frontmatter (name, description, metadata)
3. Claude Code reads this content as instructions
4. Claude Code follows the instructions to:
   - Ask for your input
   - Classify the agent
   - Prepare the API request
   - Execute the request

The skill is purely instructional—no code execution, no plugins, just clear markdown that Claude Code understands.

## Learn More

- **Paperclip API:** The API creates issues and manages agent assignments at `http://127.0.0.1:3100/api`
- **Agent IDs:** Designer, Engineer, DevOps, QA, CEO (see classification rules above)
- **Issue Format:** Title + description + priority + assignee → full workflow
- **Update Survival:** This skill survives Claude Code updates because it's in `~/.claude/skills/` (covered by the Customization Strategy)

---

**Questions?** Check the CLAUDE.md or PAPERCLIP_TEAM_INDEX.md files in the project root.

**Ready to use?** Try: `/dispatch-to-agents "Your first issue"`

---

*Skill Version: 1.0 | Last Updated: April 3, 2026*
