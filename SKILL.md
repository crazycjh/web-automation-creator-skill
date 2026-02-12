---
name: web-automation-skill-creator
description: Create new web automation skills for frequently-used websites. Use when the user explicitly asks to "create a skill for [website]", "build an automation for [website]", or "make a skill to automate [website]". This meta-skill guides the AI through exploration, optimization, skill creation, and testing phases to produce a reusable, token-efficient automation skill.
---

# Web Automation Skill Creator

Create optimized, reusable skills for automating web interactions.

**IMPORTANT:** This meta-skill builds upon and strictly follows the built-in `skill-creator` standards. All generated skills must comply with skill-creator requirements for structure, naming, documentation, and packaging.

## When to Use

Trigger this skill when the user says:
- "幫我為 XXX 網站建立一個 skill"
- "Create a skill for [website]"
- "Build an automation for [website]"
- "Make a skill to automate [task on website]"

## Workflow Overview

This meta-skill guides you through 6 phases:
1. **Discovery** - Understand the task and get examples
2. **Exploration** - Navigate the website and record the process
3. **Optimization** - Find minimal snapshot ranges and stable selectors
4. **Creation** - Generate skill files following skill-creator standards
5. **Testing** - Validate and iterate until working
6. **Completion** - Package using skill-creator validation

## Built-in Skill-Creator Integration

**This meta-skill uses:**
- `init_skill.py` - Initialize skill structure
- `package_skill.py` - Validate and package skills
- Skill-creator standards - Naming, structure, documentation

**Location:** `$(npm root -g)/openclaw/skills/skill-creator/`

**Key principles from skill-creator:**
- Concise is key (SKILL.md < 500 lines)
- Progressive disclosure (details in references/)
- Proper YAML frontmatter (name + description only)
- Description includes triggering conditions
- No extraneous files (README, CHANGELOG, etc.)

## Phase 1: Discovery

**Ask the user:**
1. What website/task do you want to automate?
2. What's a typical example of this task?
3. What parameters vary each time? (dates, locations, quantities, etc.)

**Example:**
```
You: "I'll help you create a skill for [website]. Can you give me a typical example of what you'd search for?"
User: "Search for buses from Gdansk to Warsaw on 2/12"
```

## Phase 2: Exploration

**Execute the task manually while observing:**

1. Open the target website using browser tool
2. Take initial snapshot with `maxChars=8000, refs="aria"`
3. Perform each step of the task:
   - Fill forms
   - Click buttons
   - Wait for dynamic content
   - Navigate through pages
4. **Record for each step:**
   - Element selectors (aria-label, placeholder, button text)
   - Wait times needed
   - Dialog/modal appearances
   - Dynamic content loading

**Key observations to note:**
- Which elements have stable identifiers (aria-label, id, placeholder)?
- What's the minimal UI region needed for each step?
- How long do autocomplete/loading states take?
- Are there any tricky interactions (date pickers, multi-step forms)?

## Phase 3: Optimization

**Test different snapshot strategies:**

1. **Find minimal snapshot range:**
   - Try `maxChars=6000` - Does it capture essential elements?
   - Try `maxChars=8000` - Better coverage?
   - Try `maxChars=10000` - Only if needed
   - **Goal:** Smallest value that reliably captures the UI

2. **Identify stable selectors:**
   - Prefer: `aria-label`, `placeholder`, button text
   - Avoid: Generic class names, position-based selectors
   - Test: Do refs stay consistent across page loads?

3. **Optimize wait times:**
   - Autocomplete: Usually 1500-2000ms
   - Page navigation: Usually 2000-3000ms
   - Dynamic content: Test and adjust

**Document findings in a notes section.**

## Phase 4: Creation

**IMPORTANT:** Follow the built-in skill-creator standards to ensure compliance.

### 4.1 Initialize Skill

Use the built-in init_skill.py script:

```bash
cd $HOME/.openclaw/workspace
python3 $(npm root -g)/openclaw/skills/skill-creator/scripts/init_skill.py [skill-name] --path skills --resources scripts,references
```

**Naming conventions (from skill-creator):**
- Use lowercase letters, digits, and hyphens only
- Format: `[website]-[action]` (e.g., `google-flights`, `flixbus-search`)
- Keep under 64 characters
- Name the folder exactly after the skill name

### 4.2 Write SKILL.md

**Follow skill-creator standards:**

#### Frontmatter (REQUIRED)
```yaml
---
name: [skill-name]
description: [Comprehensive description including WHEN to use this skill]
---
```

**Critical:** The description is the PRIMARY TRIGGERING MECHANISM. Include:
- What the skill does
- When to use it (specific triggers)
- Example user queries that should trigger it

**Example:**
```yaml
description: Search and query flight prices on Google Flights. Use when the user asks to search for flights, check flight prices, find cheap flights, or compare flight options between cities/airports. Supports round-trip and one-way searches with date and passenger options.
```

#### Body Structure

**Keep SKILL.md under 500 lines** (skill-creator principle: "Concise is Key")

```markdown
# [Skill Name]

[1-2 sentence description]

## Quick Start

[Minimal example showing typical usage]

## Workflow

### Step 1: [Action]
[Specific instructions with selectors]

### Step 2: [Action]
[Continue...]

## Token Optimization

**Critical strategies:**
- Use maxChars=[value]
- Use refs="aria"
- [Other optimizations]

## Parameters

- **param1**: Description
- **param2**: Description

## Troubleshooting

[Common issues]

## Reference Files

- `references/selectors.md` - Detailed selectors and optimization strategies
```

**Progressive Disclosure Principle:**
- Essential workflow in SKILL.md
- Detailed information in references/
- Move lengthy content to references/ if SKILL.md approaches 500 lines

### 4.3 Create Bundled Resources

**Follow skill-creator resource guidelines:**

#### references/ (Documentation loaded as needed)
- **Purpose:** Reference material loaded into context when needed
- **Content:** Detailed selectors, troubleshooting guides, examples
- **Best practice:** Keep SKILL.md lean, move details here
- **Structure:** One level deep from SKILL.md (avoid nested references)

**Example: references/selectors.md**
```markdown
# Element Selectors

## Key Form Elements
- Destination field: `combobox[aria-label*="要去哪裡"]`
- Date fields: `textbox[placeholder="去程"]`
...

## Optimization Strategies
- Use maxChars=6000-8000
- Use refs="aria"
...
```

#### scripts/ (Optional - Executable code)
- **When to include:** Deterministic operations, repeated code patterns
- **Purpose:** Token-efficient execution without loading into context
- **Examples:** Workflow planners, data transformers
- **Note:** Scripts may still be read by AI for adjustments

**Only create scripts if truly needed** - Don't add unnecessary complexity.

#### assets/ (Optional - Output resources)
- **When to include:** Files used in output (templates, images)
- **Not for:** Documentation (use references/ instead)
- **Web automation:** Usually not needed

### 4.4 Make Scripts Executable
```bash
chmod +x skills/[skill-name]/scripts/*
```

### 4.5 Validate Structure

**Check compliance with skill-creator standards:**
- [ ] SKILL.md has proper YAML frontmatter (name + description only)
- [ ] Description includes triggering conditions
- [ ] SKILL.md body is under 500 lines
- [ ] No extraneous files (README.md, CHANGELOG.md, etc.)
- [ ] Resource directories only created if needed
- [ ] References are one level deep from SKILL.md

## Phase 5: Testing

**Execute the skill workflow:**

1. Follow the SKILL.md instructions step-by-step
2. Use the documented selectors and parameters
3. Apply the optimization strategies

**If it fails:**
1. Go back to "no rules" mode - operate the website manually again
2. Compare with the documented workflow
3. Identify what changed or what was missed
4. Update the skill files
5. Test again

**Iteration limit:** If it fails 3 times, report to the user:
- What's working
- What's failing
- Possible reasons
- Whether manual intervention is needed

**Success criteria:**
- Complete workflow executes without errors
- Results are captured correctly
- Token usage is optimized
- Selectors are stable

## Phase 6: Completion

**When successful:**

### 6.1 Package the Skill

**Use the built-in package_skill.py script** (includes automatic validation):

```bash
python3 $(npm root -g)/openclaw/skills/skill-creator/scripts/package_skill.py $HOME/.openclaw/workspace/skills/[skill-name]
```

**The script will:**
1. Validate YAML frontmatter format
2. Check skill naming conventions
3. Verify directory structure
4. Validate description completeness
5. Check file organization
6. Create .skill file (zip with .skill extension)

**If validation fails:**
- Fix reported errors
- Run package_skill.py again
- Do NOT manually create zip files

**If validation passes:**
- Creates `[skill-name].skill` in the skill directory
- Ready for distribution and use

### 6.2 Report to User

```
✅ [Skill Name] skill created and tested successfully!

**Location:** skills/[skill-name]/
**Package:** skills/[skill-name].skill

**Compliance:** Validated against skill-creator standards
- ✅ Proper YAML frontmatter
- ✅ Comprehensive description with triggers
- ✅ SKILL.md under 500 lines
- ✅ Proper resource organization
- ✅ No extraneous files

**Usage:** Just say "[natural language trigger]" and I'll automatically use this skill.

**Example:** "[example usage based on description]"

**Token Optimization:** ~[X]% reduction compared to unoptimized approach
```

## Best Practices

### Token Efficiency
- Always use maxChars parameter on snapshots
- Use refs="aria" for stable references
- Snapshot only necessary UI regions
- Avoid full-page snapshots when possible

### Selector Stability
- Prefer semantic attributes (aria-label, placeholder)
- Use button text for clickable elements
- Avoid position-based or class-based selectors
- Test selectors across multiple runs

### Error Handling
- Document common failure modes
- Provide troubleshooting steps
- Include fallback strategies
- Note language/locale dependencies

### Documentation
- Keep SKILL.md concise and actionable
- Use references/ for detailed information
- Include real examples
- Document optimization rationale

## Common Patterns

### Form Filling
1. Click input field
2. Type value
3. Wait for autocomplete (1500ms)
4. Select from suggestions

### Date Selection
- Option A: Type YYYY-MM-DD directly
- Option B: Click calendar and select date
- Choose based on reliability

### Search and Results
1. Fill search form
2. Click search button
3. Wait for results (2000-3000ms)
4. Snapshot results with maxChars=8000

### Multi-Step Flows
- Break into clear phases
- Document state transitions
- Note when dialogs open/close
- Track which elements become active

## Troubleshooting

**Selectors not found:**
- Website may have changed
- Language/locale mismatch
- Element not yet loaded (increase wait time)

**Snapshot too large:**
- Reduce maxChars
- Target specific elements
- Use element parameter if supported

**Inconsistent behavior:**
- Add longer wait times
- Check for dynamic content
- Verify element states (active, disabled)

## Reference

See `references/methodology.md` for detailed methodology and examples.
