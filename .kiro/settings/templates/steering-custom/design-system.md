# Design System Steering

## Purpose
Document Figma design system configuration and component structure for AI agents to reference during specification generation.

---

## Figma Files

### Primary Design System
- **File Name**: [e.g., "ProductName Design System"]
- **Figma URL**: `https://www.figma.com/file/{FILE_KEY}/{FILE_NAME}`
- **File Key**: `{FILE_KEY}`
- **Last Reviewed**: [YYYY-MM-DD]

### Component Libraries (if multiple)
- **Library 1**: [Name] - [Figma URL]
  - Purpose: [e.g., Core UI components]
- **Library 2**: [Name] - [Figma URL]
  - Purpose: [e.g., Marketing components]

### Additional Files
- **Feature Designs**: [Figma project/folder URL]
- **Prototypes**: [Figma URL or location]

---

## Component Organization

### Figma Page Structure
```
[Describe your Figma file structure]
Example:
├── Foundations (Colors, Typography, Spacing)
├── Components
│   ├── Atoms (Button, Input, Icon)
│   ├── Molecules (Card, FormField)
│   └── Organisms (Header, Footer)
└── Templates (Page layouts)
```

### Naming Conventions
**Component Naming**:
- Pattern: `[Category]/[ComponentName]/[Variant]`
- Example: `Button/Primary/Large`, `Input/Text/Default`

**Frame Naming**:
- Pattern: `[PageName]-[SectionName]-[State]`
- Example: `Dashboard-Overview-Default`, `Login-Form-Error`

---

## Design Tokens

### Token Naming Rules
**Figma Style → Code Token Mapping**:
- Colors: `[Semantic]/[Shade]` → `--color-[semantic]-[shade]`
  - Example: `Primary/500` → `--color-primary-500`
- Typography: `[Role]/[Size]` → `--text-[role]-[size]`
  - Example: `Heading/H1` → `--text-heading-h1`
- Spacing: `[Size]` → `--spacing-[size]`
  - Example: `Medium` → `--spacing-md`
- Effects: `[Type]/[Size]` → `--[type]-[size]`
  - Example: `Shadow/Small` → `--shadow-sm`

### Token Categories
**List your token structure**:
- Colors: [e.g., Primary, Secondary, Neutral, Success, Warning, Error]
- Typography: [e.g., Font families and scale]
- Spacing: [e.g., 4px base scale: xs, sm, md, lg, xl]
- Effects: [e.g., Shadows, border radius]

---

## Component Mapping Rules

### General Principles
**Figma Component Set → Code Component**:
- One Figma component set maps to one code component with variant props
- Example: `Button/{Primary|Secondary}/{Small|Large}` → `<Button variant="primary" size="large" />`

**Property Mapping**:
- Figma boolean properties → Boolean props
- Figma variant properties → Enum/union type props
- Figma instance swap → Render prop or slot pattern

### Naming Conversion
- Figma: `PascalCase/Variant/Modifier`
- Code: `PascalCase` component with `camelCase` props
- Example: `TextInput/Email/Error` → `<TextInput type="email" state="error" />`

---

## Design Token Implementation

### Approach
**Strategy**: [e.g., CSS Custom Properties with TypeScript types]

**Rationale**: [Why this approach fits your project]

**Generation Workflow**:
1. Extract design tokens from Figma using MCP during design phase
2. Generate CSS custom properties and TypeScript definitions
3. Components consume tokens via [CSS variables / theme object / etc.]

---

## Notes

### Maintenance
- Review this document when major design system changes occur
- Update Figma URLs if files are moved or renamed
- Keep token naming conventions current

### For AI Agents
- This file is loaded as project memory by all spec agents
- Figma MCP tools will use URLs and file keys from this document
- Token mappings guide design-to-code translation

---

## References
- Figma MCP Setup: `.kiro/docs/figma-mcp-setup.md`
- Discovery Process: `.kiro/settings/rules/design-discovery-figma.md`
