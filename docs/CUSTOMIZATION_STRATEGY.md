# 🔧 Claude Code Customization Strategy for Fortuenti

**Goal:** Customize Claude Code without breaking it or losing changes on updates

---

## ⚠️ The Problem

If you modify Claude Code's source code directly:
- ❌ Updates will overwrite your changes (conflict/loss)
- ❌ Merging updates becomes painful (git conflicts)
- ❌ Hard to maintain two versions (yours + upstream)
- ❌ Can't easily contribute back to Anthropic
- ❌ Makes debugging harder (which version is running?)

---

## ✅ The 4 Best Solutions (Ranked)

### Option 1: MCP Server Only (RECOMMENDED) ⭐⭐⭐⭐⭐

**The Idea:** Don't modify Claude Code at all. Keep all customizations in a separate MCP server + config files.

**What it looks like:**
```
/Users/ricardoiguaran/Desktop/CC/
├── claude-code/                    (Vanilla Anthropic code)
│   └── (No modifications)
│
└── fortuenti-mcp-server/           (Your customizations)
    ├── src/tools/workflowTools.ts
    ├── src/tools/userTools.ts
    └── ...
```

**How it works:**
```json
// ~/.claude/settings.json
{
  "mcpServers": {
    "fortuenti-api": {
      "type": "stdio",
      "command": "node",
      "args": ["/Users/ricardoiguaran/Desktop/CC/fortuenti-mcp-server/dist/index.js"]
    }
  }
}
```

**When Claude Code updates:**
1. Delete old `/claude-code/` directory
2. Clone fresh version
3. Your MCP server still works (it's separate)
4. No conflicts, no merge pain

**Pros:**
- ✅ Zero breaking changes on updates
- ✅ Clean separation of concerns
- ✅ Easy to maintain
- ✅ Can upgrade Claude Code instantly
- ✅ Your code survives any upstream changes
- ✅ Easy to test independently
- ✅ Can share MCP server with others

**Cons:**
- Only works for API/tool extensions (not UI modifications)
- Can't customize Claude Code's UI/UX directly

**Best for:** Fortuenti's situation (we only need to add tools, not modify core)

---

### Option 2: Custom Skills + Plugins ⭐⭐⭐⭐

**The Idea:** Use Claude Code's built-in skill system to customize behavior without modifying code.

**What it looks like:**
```
~/.claude/skills/fortuenti/
├── setup-client-account/
│   └── SKILL.md
├── debug-workflow/
│   └── SKILL.md
└── optimize-workflow/
    └── SKILL.md
```

**How it works:**

Skills are Markdown files that Claude reads and executes. Example:

```markdown
# Setup Client Account

<!-- claude-skill
name: setup-fortuenti-client
description: Onboard a new client in 5 minutes
-->

This skill automates the setup process:
1. Create workspace
2. Configure API keys
3. Deploy first workflow
4. Add team members
```

**When Claude Code updates:**
- Skills stay in `~/.claude/skills/`
- Completely isolated from code updates
- Survives any version change

**Pros:**
- ✅ No code modifications needed
- ✅ Skills survive updates forever
- ✅ Non-technical people can create skills
- ✅ Easy to version control
- ✅ Can package as marketplace items
- ✅ Fortuenti-branded workflows

**Cons:**
- Skills are static (no dynamic customization)
- Can't modify Claude Code's core behavior

**Best for:** Pre-built workflows, automation templates, customer onboarding

---

### Option 3: Fortuenti Edition (White-Label Fork) ⭐⭐⭐

**The Idea:** Fork Claude Code on GitHub, maintain your own branded version with customizations.

**What it looks like:**
```
github.com/fortuenti/claude-code/  (Your fork)
├── src/
│   ├── (Anthropic's original code)
│   └── (Fortuenti customizations)
├── branding/
│   └── fortuenti-logo.png
└── .github/workflows/
    └── sync-upstream.yml  (Auto-merge Anthropic updates)
```

**How it works:**

1. Fork Claude Code on GitHub
2. Make customizations (UI, tools, branding)
3. Use GitHub Actions to auto-merge Anthropic's updates
4. Resolve conflicts manually (usually simple)
5. Ship as "Fortuenti Developer Assistant"

**Merge strategy:**
```bash
# Add upstream remote
git remote add upstream https://github.com/anthropics/claude-code.git

# Periodically merge updates
git fetch upstream
git merge upstream/main  # Resolve conflicts if any
git push origin main
```

**Pros:**
- ✅ Full control over UI/UX
- ✅ Can customize anything
- ✅ Can merge updates automatically (mostly)
- ✅ Distributable as branded product
- ✅ Can contribute changes back to Anthropic
- ✅ Clear git history of customizations

**Cons:**
- ❌ Need to manage merge conflicts (usually 2-3 per update)
- ❌ More maintenance overhead
- ❌ Can't easily switch back to vanilla Claude Code
- ❌ Requires git expertise
- ❌ Could fall behind upstream if updates are frequent

**Best for:** If you need UI customizations, branding, or distributing as a product

---

### Option 4: Wrapper CLI (Docker/Npm) ⭐⭐⭐

**The Idea:** Create a wrapper script that launches vanilla Claude Code with your customizations injected.

**What it looks like:**
```bash
# /usr/local/bin/fortuenti-code
#!/bin/bash

# Load Fortuenti config
export FORTUENTI_API_TOKEN=$(cat ~/.fortuenti/api_token)
export FORTUENTI_MCP_SERVER="/path/to/mcp-server"

# Create custom settings
cat > ~/.claude/settings.json << EOF
{
  "mcpServers": {
    "fortuenti": {
      "type": "stdio",
      "command": "node",
      "args": ["$FORTUENTI_MCP_SERVER"]
    }
  }
}
EOF

# Launch vanilla Claude Code
exec npx @anthropic-ai/claude-code "$@"
```

**When Claude Code updates:**
- Run `npm update -g @anthropic-ai/claude-code`
- Your wrapper automatically picks up the new version
- No conflicts, fully transparent

**Pros:**
- ✅ Zero maintenance on Claude Code updates
- ✅ Instant access to new versions
- ✅ Can customize startup behavior
- ✅ Can inject environment variables
- ✅ Can auto-configure settings
- ✅ Works with global npm install

**Cons:**
- Can only customize via CLI args/env vars/config files
- Can't customize core behavior

**Best for:** Internal usage, automated deployments, minimal customization

---

## 🎯 Recommendation: Hybrid Approach

**For Fortuenti, I recommend combining Options 1 + 2 + 4:**

```
~/.claude/
├── config.json              # Standard Claude Code config
├── settings.json            # MCP servers + skills
├── fortuenti-setup.sh       # Custom launcher script
└── skills/fortuenti/        # Pre-built skills
    ├── setup-client/
    ├── debug-workflow/
    └── optimize-workflow/

/path/to/fortuenti-mcp-server/  # Your tools (completely separate)
├── src/
│   ├── tools/
│   │   ├── workflowTools.ts
│   │   ├── userTools.ts
│   │   └── ...
│   └── ...
```

**Update strategy:**
1. Delete old Claude Code folder
2. Clone/install fresh version
3. MCP server still works (it's separate)
4. Skills are in `~/.claude/` (survives updates)
5. Config files are in `~/.claude/` (survives updates)
6. Everything just works

**Zero breaking changes, zero merge conflicts.**

---

## 🚀 Implementation: Custom Launcher Script

Here's how to set it up:

### Step 1: Create wrapper script
```bash
#!/bin/bash
# ~/.local/bin/fortuenti-code

# Load Fortuenti settings
export FORTUENTI_API_TOKEN=$(cat ~/.fortuenti/api_token 2>/dev/null || echo "")
export FORTUENTI_API_URL=${FORTUENTI_API_URL:-"http://localhost:8000"}

# Ensure ~/.claude/settings.json has Fortuenti MCP server
mkdir -p ~/.claude/skills/fortuenti

if [ ! -f ~/.claude/settings.json ]; then
  cat > ~/.claude/settings.json << 'EOF'
{
  "mcpServers": {
    "fortuenti-api": {
      "type": "stdio",
      "command": "node",
      "args": ["/Users/ricardoiguaran/Desktop/CC/fortuenti-mcp-server/dist/index.js"]
    }
  }
}
EOF
  echo "✅ Created ~/.claude/settings.json with Fortuenti MCP server"
fi

# Launch Claude Code
exec npx @anthropic-ai/claude-code "$@"
```

### Step 2: Make it executable
```bash
chmod +x ~/.local/bin/fortuenti-code
export PATH=$PATH:~/.local/bin
```

### Step 3: Use it
```bash
fortuenti-code  # Launches Claude Code with Fortuenti config pre-loaded
```

### Step 4: Update Claude Code anytime
```bash
npm update -g @anthropic-ai/claude-code
fortuenti-code  # Just works with new version
```

---

## 📋 Comparison Table

| Approach | Customization | Update Safety | Effort | Best For |
|----------|---------------|---------------|--------|----------|
| **MCP Server Only** | Tools/APIs | ✅✅✅ Perfect | Low | APIs, tools, extensions |
| **Skills + Plugins** | Workflows | ✅✅✅ Perfect | Low | Pre-built automation |
| **White-Label Fork** | Everything | ✅ Moderate | High | Full branding/UI |
| **Wrapper CLI** | Startup behavior | ✅✅✅ Perfect | Low | Configuration injection |
| **Hybrid (Recommended)** | Tools + Workflows | ✅✅✅ Perfect | Medium | Fortuenti's use case |

---

## 🛡️ Git Strategy for Your MCP Server

Since your MCP server IS separate from Claude Code, it's simple:

```bash
cd /Users/ricardoiguaran/Desktop/CC/fortuenti-mcp-server

# Initialize git
git init
git remote add origin https://github.com/fortuenti/mcp-server.git

# Regular commits
git add src/
git commit -m "Add user management tools"
git push origin main

# When Anthropic updates Claude Code, you just:
# 1. Delete old claude-code/
# 2. Clone fresh version
# 3. Your git repo is untouched
```

**No conflicts, no merge pain. Ever.**

---

## ⚡ The Winning Strategy

**Don't modify Claude Code at all.**

Instead:

1. **Keep Claude Code vanilla** (delete and reinstall anytime)
2. **Build everything in your MCP server** (separate, maintainable)
3. **Configure via `~/.claude/settings.json`** (survives updates)
4. **Create skills in `~/.claude/skills/`** (survives updates)
5. **Use wrapper script** (auto-setup on launch)

**Result:**
- ✅ Claude Code updates = zero friction
- ✅ Your customizations persist forever
- ✅ Clean separation of concerns
- ✅ Easy to test independently
- ✅ Can share your MCP server with others
- ✅ Zero git conflicts

---

## 📝 Action Items

**This Week:**
- [ ] Finalize MCP server in `/fortuenti-mcp-server/` (you're already doing this)
- [ ] Initialize git repo for MPC server
- [ ] Create wrapper script (`~/.local/bin/fortuenti-code`)
- [ ] Test that Claude Code + MCP server works together

**By Next Week:**
- [ ] Document custom setup for team
- [ ] Create onboarding guide for new engineers
- [ ] Publish MCP server to npm (optional)

**Going Forward:**
- Update Claude Code whenever Anthropic releases updates
- Never modify Claude Code source
- All customizations live in:
  - `/fortuenti-mcp-server/` (tools)
  - `~/.claude/skills/` (workflows)
  - `~/.claude/settings.json` (config)
  - Wrapper script (startup behavior)

---

## 🎯 Final Word

**The key insight:** Don't view Claude Code as something to customize. View it as a platform to extend.

Extend via:
- ✅ MCP servers (tools)
- ✅ Skills (workflows)
- ✅ Config files (settings)
- ✅ Wrapper scripts (startup)

Never via:
- ❌ Direct code modifications
- ❌ Patching source files
- ❌ Forking and diverging

**This way, you get:**
- All of Anthropic's improvements (instantly)
- All of your customizations (persisting forever)
- Zero maintenance burden (clean separation)

**Best of both worlds.** 🚀
