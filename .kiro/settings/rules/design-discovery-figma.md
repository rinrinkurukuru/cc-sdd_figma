# Figma Discovery Process for UI Design

## Objective
Extract design data from Figma to ensure UI component design is based on actual design system specifications and prototypes.

## When to Apply Figma Discovery

**Execute Figma discovery when**:
- Feature involves UI/frontend components (screens, pages, forms, navigation)
- `requirements.md` contains Design References section with Figma URLs
- `.kiro/steering/design-system.md` exists with Figma file information
- Project description includes UI-related keywords

**Skip Figma discovery for**:
- Backend-only features (APIs, databases, business logic)
- CLI tools or batch processes
- Infrastructure changes without UI impact

## Discovery Steps

### 1. Figma File Information Gathering

**Load Figma Context**:
- Check `.kiro/steering/design-system.md` for Figma file URLs and organization structure
- Extract Figma references from `requirements.md` Design References section
- Parse Figma file key from URL: `https://www.figma.com/file/{FILE_KEY}/{FILE_NAME}`
- Extract node IDs if specific frames are referenced: `?node-id={NODE_ID}`

### 2. File Metadata Extraction

**Use `mcp__figma__get_file`**:
- Retrieve file metadata, last updated timestamp, page structure
- Identify relevant pages and frames for the feature
- Map page organization to screen/component structure

**Document**:
- File name and key
- Relevant pages and frames
- Last updated date (for version tracking)

### 3. Component Inventory Analysis

**Use `mcp__figma__get_component_sets`**:
- Extract all component definitions and component sets
- Identify component variants (size, state, type properties)
- Document component hierarchy and relationships

**Analyze**:
- Component naming conventions (e.g., `Button/Primary/Large`)
- Variant properties and their values
- Default values for each property
- Component dependencies and composition patterns

**Map to Code**:
- Figma component name → Code component name
- Figma variant properties → Component props
- Figma boolean properties → Boolean props
- Figma instance swap → Render props or slots

### 4. Design Token Extraction

**Extract from Figma Styles**:

**Color Tokens**:
- Color styles with names and values (RGB/HEX)
- Example: `Primary/500: #3B82F6`, `Neutral/100: #F3F4F6`

**Typography Tokens**:
- Text styles: font family, size, weight, line height, letter spacing
- Example: `Heading/H1: Inter, 32px, Bold, 40px line-height`

**Spacing Tokens**:
- Auto Layout spacing values from components
- Example: `spacing-xs: 4px`, `spacing-md: 16px`

**Effect Tokens**:
- Shadow and blur effects
- Example: `Shadow/Small: 0px 1px 2px rgba(0,0,0,0.05)`

**Document Token Mapping**:
- Figma style name → CSS variable/design token name
- Example: `Primary/500` → `--color-primary-500` or `theme.colors.primary[500]`

### 5. Prototype Analysis (if applicable)

**When prototypes exist**:
- Identify interaction triggers (click, hover, keyboard)
- Map screen transition flows (source → destination)
- Document conditional navigation logic
- Extract UI state requirements (modals, tabs, forms)

**Derive State Management Needs**:
- Modal open/close states
- Tab active states
- Form validation states
- Loading and error states

### 6. Specific Node Details (optional)

**Use `mcp__figma__get_file_nodes`** when:
- Specific frames referenced by node ID
- Detailed layout structure needed
- Analyzing Auto Layout constraints

**Extract**:
- Node hierarchy (parent-child relationships)
- Layout properties (padding, spacing, alignment)
- Constraints and responsive behavior

## Integration with Design Document

**Map Figma data to design.md sections**:

### Components & Interfaces
Add "Figma Component Mapping" subsection for each UI component:

```markdown
**Figma Component Mapping**:
- **Figma Component**: Button/Primary/Large
- **Figma File**: [Design System](https://figma.com/file/abc123)
- **Variants**: Primary, Secondary, Outline (variant), Small, Medium, Large (size)

**Property Mapping**:
| Figma Property | Code Property | Type | Values |
|----------------|---------------|------|--------|
| Variant | variant | enum | 'primary' \| 'secondary' \| 'outline' |
| Size | size | enum | 'small' \| 'medium' \| 'large' |

**Design Tokens Used**:
- Colors: `--color-primary-500`, `--color-neutral-100`
- Spacing: `--spacing-md` (padding)
```

### Data Models
Add "UI State Model (from Figma)" subsection:

```typescript
// Modal State (from Figma prototype interactions)
interface ModalState {
  isOpen: boolean;
  modalType: 'confirmation' | 'form' | 'alert';
}

// Navigation State (from Figma navigation flows)
interface NavigationState {
  activeTab: 'dashboard' | 'analytics' | 'settings';
}
```

### Technology Stack / Design Decisions
Document design token implementation strategy:

```markdown
**Design Token Implementation**:
- Approach: CSS Custom Properties with TypeScript types
- Rationale: Single source of truth, runtime theming support
- Generation: Extract from Figma → CSS variables + TS types
```

## Fallback Strategy

**If Figma MCP tools are unavailable**:
1. Log warning message about missing Figma integration
2. Proceed with manual design references from `requirements.md`
3. Include placeholders in design document for manual Figma data entry
4. Continue design generation without blocking

**Error Handling**:
- **Authentication failed**: Verify Personal Access Token configuration
- **File not found**: Check Figma file URL and token permissions
- **Rate limit exceeded**: Implement retry logic or reduce requests

## Output Requirements

**Include in design document**:
- Component inventory with Figma mappings
- Design token definitions and CSS variable mappings
- UI state model derived from prototypes
- Figma file references with last updated timestamps

**Command output summary**:
```markdown
**Figma Discovery Executed**:
- File: [Design System] (abc123)
- Components Extracted: 24
- Design Tokens: Colors (12), Typography (8), Spacing (6), Effects (4)
- Prototypes Analyzed: 3 flows
- Key Findings: [2-3 critical insights for implementation]
```

## Best Practices

**Token Naming Consistency**:
- Establish clear mapping rules: Figma style → code token
- Use hierarchical naming: primitive → semantic → component tokens

**Component Granularity**:
- Map one Figma component set → one code component with props
- Break down complex Figma components into sub-components if needed

**Version Tracking**:
- Record Figma file last updated timestamp in design document
- Enable detection of design-code drift over time

**Prototype Interpretation**:
- Figma prototypes show design intent, not exact implementation logic
- Use prototypes to inform state machines and event handling design
- Cross-reference with requirements for validation

## Reference

- Figma MCP Setup: `.kiro/docs/figma-mcp-setup.md`
- Design Template: `.kiro/settings/templates/specs/design.md`
- Steering Template: `.kiro/settings/templates/steering-custom/design-system.md`
