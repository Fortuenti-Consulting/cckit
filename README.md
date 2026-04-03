# CCKit

**Safely customize Claude Code. Updates won't break your customizations.**

## The Problem

You want to extend Claude Code for your company. Add custom tools, create new slash commands, integrate your APIs. But you're terrified of one thing:

> "What happens when Claude Code updates? Will my customizations break? Will I lose everything?"

Most approaches fail because they modify Claude Code's source. When updates come, merge conflicts destroy your work.

## The Solution

CCKit is a **proven system** for customizing Claude Code that:
- ✅ **Survives all updates** - Your customizations persist through every Claude Code release
- ✅ **Zero merge conflicts** - Customizations live separately from Claude Code
- ✅ **Production-tested** - Built and validated by Fortuenti
- ✅ **MIT licensed** - Free to use, modify, redistribute
- ✅ **Complete documentation** - Step-by-step guides included

## How It Works

Instead of modifying Claude Code, CCKit uses Claude Code's built-in extension points:

```
Your Customizations
    ├─ MCP Servers (separate git repo)
    ├─ Skills (in ~/.claude/skills/)
    ├─ Configuration (in ~/.claude/settings.json)
    └─ Wrapper Script (in ~/.local/bin/)

Claude Code
    ├─ Auto-discovers your MCP servers
    ├─ Auto-indexes your skills
    ├─ Loads your config
    └─ Updates don't touch any of the above
```

**Result:** Claude Code updates freely. Your customizations persist forever.

## Quick Start

### 1. Read the Strategy
```bash
See: docs/CUSTOMIZATION_STRATEGY.md
```

### 2. Implement the System
```bash
See: docs/SETUP_GUIDE.md
```

### 3. Try the Example
```bash
See: examples/dispatch-to-agents/
```

### 4. Extend with Your Own Tools
```bash
See: docs/BUILD_YOUR_OWN.md
```

## What's Included

| File | Purpose |
|------|---------|
| `docs/CUSTOMIZATION_STRATEGY.md` | Complete technical strategy (4 approaches) |
| `docs/SETUP_GUIDE.md` | Step-by-step implementation guide |
| `docs/BUILD_YOUR_OWN.md` | How to create your own MCP servers & skills |
| `examples/dispatch-to-agents/` | Working example skill |
| `examples/mcp-server/` | Working example MCP server |

## Why Use CCKit?

### vs. Building It Yourself
- **Time:** You could spend months figuring this out
- **Risk:** Easy to get wrong, customizations break on updates
- **Maintenance:** Updates to Claude Code require constant re-engineering

### vs. Modifying Claude Code Source
- **Conflicts:** Merge conflicts destroy your work on updates
- **Fragile:** One update breaks everything
- **Unsustainable:** Maintenance nightmare

### vs. Forking Claude Code
- **Massive effort:** Fork has 380K lines of code
- **Constant merge hell:** Every update requires manual merging
- **Dead weight:** You maintain code you don't need

## Real-World Example

**Before CCKit:**
```
Month 1:  Build custom MCP server for your APIs
Month 2:  Create slash commands for your team
Month 3:  Deploy to production, team loves it
Month 4:  Claude Code updates... oh no
Month 5:  Merge conflicts everywhere
Month 6:  Give up, rebuild manually
```

**With CCKit:**
```
Week 1:  Learn the strategy
Week 2:  Build custom MCP server for your APIs
Week 3:  Create slash commands for your team
Week 4:  Deploy to production, team loves it
Ongoing: Claude Code updates, your customizations still work ✅
```

## Who Is This For?

- **Companies** extending Claude Code for internal use
- **Agencies** building custom Claude Code versions for clients
- **Enterprises** needing guaranteed update compatibility
- **Developers** who want to extend Claude Code without fear

## Getting Started

1. **Clone this repo**
   ```bash
   git clone https://github.com/fortuenti/cckit.git
   cd cckit
   ```

2. **Read the strategy**
   ```bash
   cat docs/CUSTOMIZATION_STRATEGY.md
   ```

3. **Follow the setup guide**
   ```bash
   cat docs/SETUP_GUIDE.md
   ```

4. **Look at examples**
   ```bash
   ls examples/
   ```

## Professional Services

CCKit is free and open source. You can implement it yourself.

But if you want professional help:
- **Implementation consulting** - We help you customize Claude Code for your use case ($5K-20K)
- **White-label versions** - We maintain a branded version of CCKit for your company ($50K+/year)
- **Ongoing support** - We handle updates and maintenance as Claude Code evolves

**Contact:** [contact@fortuenti.co](mailto:contact@fortuenti.co)

## Architecture Overview

```
┌─────────────────────────────────────────────────┐
│ Claude Code (Vanilla - Never Modified)          │
│                                                 │
│ On startup, auto-discovers:                    │
│ - MCP servers in ~/.fortuenti/mcp/             │
│ - Skills in ~/.claude/skills/                  │
│ - Config in ~/.claude/settings.json            │
└─────────────────────────────────────────────────┘
         ↑                ↑                ↑
         │                │                │
┌────────┴────────┐ ┌────┴──────────┐ ┌──┴──────────────┐
│ MCP Servers     │ │ Skills        │ │ Configuration  │
│ (Separate Repo) │ │ (~/.claude/)  │ │ (~/.claude/)   │
│                 │ │               │ │                │
│ Your APIs       │ │ Slash         │ │ Wrapper script │
│ Custom tools    │ │ commands      │ │ Auto-config    │
│ Third-party     │ │ Workflows     │ │ Settings       │
│ integrations    │ │ Examples      │ │                │
└─────────────────┘ └───────────────┘ └────────────────┘
     (Git)            (Git)              (Git)
```

All three can be updated independently without affecting Claude Code.
When Claude Code updates, nothing breaks.

## FAQ

**Q: Will this work with future Claude Code versions?**
A: Yes. This uses Claude Code's official extension points (MCP, skills, settings). As long as those exist (which they will), CCKit works.

**Q: Can I use this commercially?**
A: Yes. MIT license means you can use it for any purpose, including commercial.

**Q: What if I want to contribute improvements?**
A: Submit a pull request! We welcome improvements.

**Q: How do I report bugs?**
A: Open an issue on GitHub. Include your Claude Code version and what you were trying to do.

## License

MIT License - See LICENSE file

## Support

- **Documentation:** See docs/ folder
- **Examples:** See examples/ folder
- **Issues:** GitHub Issues
- **Professional help:** [contact@fortuenti.co](mailto:contact@fortuenti.co)

---

**Made by Fortuenti**

Learn more: [fortuenti.co](https://fortuenti.co)
