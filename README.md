# Web Automation Creator Skill

![Web Automation Creator Skill](img.png)

A meta-skill that guides AI assistants through creating optimized, reusable web automation skills.

**Compatible with:** OpenClaw, Kiro CLI, Claude Desktop, Cursor, and other AI assistants with skill support.

## What is this?

This is a **meta-skill** that guides AI assistants through the process of creating new web automation skills. It provides a structured methodology for:

- Exploring websites and identifying stable selectors
- Optimizing token usage with targeted snapshots
- Creating reusable, well-documented automation skills
- Testing and packaging skills for distribution

## Installation

### Choose Your AI Tool

Copy the skill folder to your tool's skills directory:

#### OpenClaw
```bash
git clone https://github.com/crazycjh/web-automation-creator-skill.git
cp -r web-automation-creator-skill/web-automation-skill-creator $HOME/.openclaw/workspace/skills/
```

#### Kiro CLI
```bash
git clone https://github.com/crazycjh/web-automation-creator-skill.git
cp -r web-automation-creator-skill/web-automation-skill-creator $HOME/.kiro/workspace/skills/
```


### Verify Installation

After copying, restart your chat session. The skill should be automatically detected when you say:
- "Create a skill for [website]"
- "幫我為 [網站] 建立一個 skill"

## Usage

Once installed, simply ask your AI assistant to create a skill for a website:

**Examples:**
- "幫我為 Google Flights 建立一個 skill"
- "Create a skill for FlixBus"
- "Build an automation for Booking.com hotel search"
- "Make a skill to automate Amazon product search"

The AI will automatically use this meta-skill to guide you through:
1. **Discovery** - Understanding your automation needs
2. **Exploration** - Testing the website workflow
3. **Optimization** - Finding efficient selectors and snapshot strategies
4. **Creation** - Generating skill files
5. **Testing** - Validating the automation

## Requirements

- OpenClaw or Kiro CLI with browser automation support

## File Structure

```
web-automation-skill-creator/
├── SKILL.md              # Main skill workflow and instructions
└── references/
    └── methodology.md    # Detailed methodology and examples
```

## What Gets Created

When you use this meta-skill, it will create a new skill with structure like:

```
your-new-skill/
├── SKILL.md              # Automation workflow
├── references/           # Detailed documentation
│   └── selectors.md
└── scripts/              # Optional helper scripts
```

## Tips

- Be specific about what you want to automate
- Provide example inputs (dates, locations, search terms)
- Test the generated skill with different inputs
- The AI will optimize for token efficiency automatically

## Troubleshooting

**Skill not triggering:**
- Make sure the folder is in `$HOME/.openclaw/workspace/skills/`
- Restart your chat session
- Check that SKILL.md has proper YAML frontmatter

**Generated skills not working:**
- Websites change frequently - selectors may need updates
- Try re-running the exploration phase
- Check the troubleshooting section in the generated skill

## Contributing

Contributions are welcome! If you:
- Improve this meta-skill
- Discover new automation patterns
- Find bugs or issues
- Have suggestions for better workflows

Please open an issue or submit a pull request on [GitHub](https://github.com/crazycjh/web-automation-creator-skill).


## Support

- **Issues:** [GitHub Issues](https://github.com/crazycjh/web-automation-creator-skill/issues)
- **Discussions:** [GitHub Discussions](https://github.com/crazycjh/web-automation-creator-skill/discussions)

---

**Star ⭐ this repo if you find it useful!**
