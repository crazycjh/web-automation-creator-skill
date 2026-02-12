# Web Automation Skill Creation Methodology

## Detailed Workflow Examples

### Example 1: Google Flights Skill (Completed)

**Phase 1: Discovery**
- Task: Search for flights between cities
- Example: "Taipei to Tokyo, 2/15-2/20, round trip"
- Parameters: origin, destination, departure date, return date, passengers, cabin class

**Phase 2: Exploration**
Key findings:
- Destination field: `combobox[aria-label*="要去哪裡"]`
- Date fields: `textbox[placeholder="去程"]` and `textbox[placeholder="回程"]`
- Search button: `button` with text "搜尋"
- Autocomplete appears after 1500ms
- Results load in 2-3 seconds

**Phase 3: Optimization**
- Optimal maxChars: 6000-8000
- Use refs="aria" for stability
- Date input: Direct typing (YYYY-MM-DD) faster than calendar clicking
- Token savings: ~80% compared to full-page snapshots

**Phase 4: Creation**
Structure:
```
google-flights/
├── SKILL.md (workflow + quick start)
├── scripts/search_flights.py (workflow planner)
└── references/selectors.md (detailed selectors)
```

**Phase 5: Testing**
- First attempt: Success
- Validated: Origin/destination selection, date input, search execution, results capture

**Result:** Fully functional skill, 80% token reduction

---

### Example 2: FlixBus Skill (Hypothetical)

**Phase 1: Discovery**
- Task: Search for bus routes
- Example: "Gdansk to Warsaw, 2/12"
- Parameters: origin, destination, date, passengers (optional)

**Phase 2: Exploration**
Expected findings:
- Origin field: Input with autocomplete
- Destination field: Input with autocomplete
- Date picker: Calendar widget or direct input
- Search button: Prominent CTA
- Results: List of departures with times and prices

**Phase 3: Optimization**
Strategies to test:
- Snapshot only search form area (not entire page)
- Test if direct typing works vs clicking autocomplete
- Measure wait times for dynamic content
- Find stable selectors (likely aria-labels or data attributes)

**Phase 4: Creation**
Recommended structure:
```
flixbus-search/
├── SKILL.md
├── references/
│   ├── selectors.md
│   └── troubleshooting.md
└── scripts/
    └── search_buses.py (optional)
```

**Phase 5: Testing**
Test cases:
1. Domestic route (same country)
2. International route
3. Next-day vs future date
4. Edge cases (no results, sold out)

---

## Selector Strategy Guide

### Priority Order (Most to Least Stable)

1. **aria-label attributes**
   - Example: `combobox[aria-label="要去哪裡？"]`
   - Pros: Semantic, accessibility-focused, usually stable
   - Cons: May be language-specific

2. **placeholder text**
   - Example: `textbox[placeholder="去程"]`
   - Pros: User-visible, less likely to change
   - Cons: Language-specific

3. **Button text**
   - Example: `button` with text "搜尋"
   - Pros: User-visible, semantic
   - Cons: Language-specific, may have multiple matches

4. **ID attributes**
   - Example: `#search-button`
   - Pros: Unique, fast lookup
   - Cons: May change with framework updates

5. **data-* attributes**
   - Example: `[data-testid="search-form"]`
   - Pros: Stable if used for testing
   - Cons: Not always present

6. **Role + name combinations**
   - Example: `button[role="button"][name="search"]`
   - Pros: Semantic, accessible
   - Cons: May not be unique

### Avoid

- Generic class names (`.btn`, `.input`)
- Position-based selectors (`:nth-child(3)`)
- Deeply nested selectors
- Framework-specific classes (`.MuiButton-root`)

---

## Token Optimization Strategies

### Snapshot Sizing

**Rule of thumb:**
- Search forms: 6000 chars
- Results pages: 8000 chars
- Complex UIs: 10000 chars
- Never exceed 12000 unless absolutely necessary

**Testing approach:**
1. Start with 6000
2. Check if all needed elements are captured
3. Increase by 2000 if missing critical elements
4. Document the optimal value

### Targeted Snapshots

Instead of full page:
```javascript
// ❌ Full page
browser(action="snapshot", refs="aria")

// ✅ Targeted
browser(action="snapshot", refs="aria", maxChars=6000)
```

### Element-Specific Snapshots

If supported:
```javascript
// Snapshot only the search form
browser(action="snapshot", selector="form[role='search']", refs="aria")
```

### Wait Instead of Snapshot

```javascript
// ❌ Multiple snapshots to check state
snapshot() → check → snapshot() → check

// ✅ Wait then snapshot once
wait(2000) → snapshot()
```

---

## Common Patterns Library

### Pattern: Autocomplete Input

**Steps:**
1. Click input field
2. Type search term
3. Wait 1500-2000ms
4. Snapshot to see suggestions
5. Click first suggestion

**Optimization:**
- Use maxChars=6000 for suggestion snapshot
- Target the dialog/dropdown if possible

**Example selector:**
```
combobox[expanded] → listbox → option[0]
```

---

### Pattern: Date Picker

**Option A: Direct Input (Preferred)**
```
1. Click date field
2. Type "YYYY-MM-DD"
3. Press Enter or Tab
```

**Option B: Calendar Widget**
```
1. Click date field
2. Wait for calendar to appear
3. Navigate to correct month
4. Click date button
```

**Choose A when:** Website accepts direct input
**Choose B when:** Direct input is blocked or unreliable

---

### Pattern: Search and Results

**Steps:**
1. Fill all search fields
2. Click search button
3. Wait 2000-3000ms for results
4. Snapshot results with maxChars=8000
5. Parse and extract key information

**Optimization:**
- Don't snapshot during loading state
- Use single wait instead of polling
- Focus snapshot on results container

---

### Pattern: Multi-Step Form

**Steps:**
1. Fill step 1 fields
2. Click "Next" or "Continue"
3. Wait for step 2 to appear
4. Fill step 2 fields
5. Continue until final step
6. Submit

**Optimization:**
- Snapshot only when needed (not every step)
- Track which step you're on
- Use stable selectors for navigation buttons

---

## Language and Locale Handling

### Strategy 1: Language-Agnostic Selectors

Prefer:
- aria-label patterns: `[aria-label*="destination"]`
- Placeholder patterns: `[placeholder*="date"]`
- Role-based: `button[role="search"]`

### Strategy 2: Multi-Language Support

Document selectors for each language:
```markdown
## Selectors

### Destination Field
- English: `combobox[aria-label*="Where to"]`
- 中文: `combobox[aria-label*="要去哪裡"]`
- 日本語: `combobox[aria-label*="目的地"]`
```

### Strategy 3: Fallback Chain

```
Try selector 1 (English)
  → If not found, try selector 2 (中文)
    → If not found, try selector 3 (generic)
```

---

## Error Recovery Patterns

### Pattern: Element Not Found

**Diagnosis:**
1. Take full snapshot (no maxChars limit)
2. Search for similar elements
3. Check if page structure changed

**Recovery:**
1. Try alternative selector
2. Increase wait time
3. Refresh page and retry
4. Report to user if persistent

### Pattern: Timeout

**Diagnosis:**
- Network slow?
- Page loading slowly?
- Element never appears?

**Recovery:**
1. Increase wait time by 50%
2. Check for error messages on page
3. Verify URL is correct
4. Try alternative approach

### Pattern: Unexpected Dialog

**Diagnosis:**
- Cookie consent?
- Login prompt?
- Survey popup?

**Recovery:**
1. Detect dialog presence
2. Close or dismiss
3. Continue with main flow
4. Document in troubleshooting section

---

## Testing Checklist

Before declaring a skill complete:

- [ ] Workflow executes end-to-end without errors
- [ ] All parameters are configurable
- [ ] Token usage is optimized (maxChars set appropriately)
- [ ] Selectors are stable across multiple runs
- [ ] Error cases are handled gracefully
- [ ] Documentation is clear and complete
- [ ] Examples are provided
- [ ] Troubleshooting section covers common issues
- [ ] Skill is packaged (.skill file created)

---

## Maintenance Guidelines

### When to Update a Skill

- Website redesign or UI changes
- Selectors stop working
- New features added to website
- Performance degradation
- User reports issues

### Update Process

1. Re-run exploration phase
2. Identify what changed
3. Update selectors in references/
4. Update workflow in SKILL.md
5. Test thoroughly
6. Update version/date in documentation
7. Re-package skill

---

## Advanced Techniques

### Dynamic Wait Times

Instead of fixed waits:
```javascript
// Wait for specific element to appear
wait until element[ref=X] is visible
```

### State Verification

Before proceeding:
```javascript
// Verify form is ready
check if search button is enabled
```

### Conditional Logic

```javascript
if (autocomplete appears) {
  select first option
} else {
  proceed with typed value
}
```

### Data Extraction

```javascript
// Extract structured data from results
results = []
for each result card:
  extract: price, time, airline, duration
  append to results
return results
```

---

## Skill Naming Conventions

**Format:** `[website]-[action]`

**Examples:**
- `google-flights` (not `flight-search`)
- `flixbus-search` (not `bus-finder`)
- `booking-hotels` (not `hotel-booking`)

**Why:** Consistent, discoverable, avoids conflicts

---

## Documentation Standards

### SKILL.md Structure

1. Frontmatter (name, description)
2. Brief introduction
3. Quick start example
4. Detailed workflow
5. Token optimization notes
6. Parameters reference
7. Troubleshooting
8. References to other files

### Keep It Concise

- SKILL.md: < 500 lines
- Focus on workflow, not theory
- Move details to references/
- Use examples over explanations

### Use Clear Language

- Imperative mood ("Click the button")
- Specific values ("Wait 1500ms")
- Concrete examples ("Tokyo" not "destination")
- Avoid jargon unless necessary
