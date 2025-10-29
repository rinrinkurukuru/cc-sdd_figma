# AI-DLC and Spec-Driven Development

Kiro-style Spec Driven Development implementation on AI-DLC (AI Development Life Cycle)

## Project Context

### Paths
- Steering: `.kiro/steering/`
- Specs: `.kiro/specs/`

### Steering vs Specification

**Steering** (`.kiro/steering/`) - Guide AI with project-wide rules and context
**Specs** (`.kiro/specs/`) - Formalize development process for individual features

### Active Specifications
- Check `.kiro/specs/` for active specifications
- Use `/kiro:spec-status [feature-name]` to check progress

## Development Guidelines
- Think in English, but generate responses in Japanese (思考は英語、回答の生成は日本語で行うように)

## Minimal Workflow
- Phase 0 (optional): `/kiro:steering`, `/kiro:steering-custom`
- Phase 1 (Specification):
  - `/kiro:spec-init "description"`
  - `/kiro:spec-requirements {feature}`
  - `/kiro:validate-gap {feature}` (optional: for existing codebase)
  - `/kiro:spec-design {feature} [-y]`
  - `/kiro:validate-design {feature}` (optional: design review)
  - `/kiro:spec-tasks {feature} [-y]`
- Phase 2 (Implementation): `/kiro:spec-impl {feature} [tasks]`
  - `/kiro:validate-impl {feature}` (optional: after implementation)
- Progress check: `/kiro:spec-status {feature}` (use anytime)

## Development Rules
- 3-phase approval workflow: Requirements → Design → Tasks → Implementation
- Human review required each phase; use `-y` only for intentional fast-track
- Keep steering current and verify alignment with `/kiro:spec-status`

## Steering Configuration
- Load entire `.kiro/steering/` as project memory
- Default files: `product.md`, `tech.md`, `structure.md`
- Custom files are supported (managed via `/kiro:steering-custom`)

## Figma MCP Integration

### Overview
Figma design data is automatically extracted during requirements and design phases for UI/frontend features.

### Setup
- **Setup Guide**: [.kiro/docs/figma-mcp-setup.md](.kiro/docs/figma-mcp-setup.md)
- **Prerequisites**: Figma Personal Access Token configured in Claude MCP settings

### Usage
**Recommended Setup** (before feature specs):
```bash
# Option 1: Use template (recommended)
/kiro:steering-custom
# Select "design-system" template and fill in Figma file URLs

# Option 2: Manual creation
/kiro:steering
# Manually create .kiro/steering/design-system.md with Figma URLs
```

**Automatic Activation**:
- UI keywords detected: `screen`, `page`, `component`, `dashboard`, `form`, `navigation`
- `.kiro/steering/design-system.md` exists with Figma URLs

**What Gets Extracted**:
- Requirements phase: Figma file metadata, component inventory, screen structure
- Design phase: Component definitions, design tokens, prototypes, UI state models

**Output**:
- `requirements.md`: Design References section with Figma URLs
- `design.md`: Figma Component Mapping, UI State Model, Design Token strategy

### Fallback
If Figma MCP unavailable, agents log warnings and proceed with manual design references.

### Reference
- Discovery rules: `.kiro/settings/rules/design-discovery-figma.md`
- Template: `.kiro/settings/templates/steering-custom/design-system.md`

