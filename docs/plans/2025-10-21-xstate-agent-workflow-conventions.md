# XState Agent Workflow Conventions

**Date:** 2025-10-21
**Status:** Research & Design Phase
**Goal:** Establish conventions for working with XState v5 in agent-based CLI workflows

## Context

XState and Stately Studio provide excellent tooling for visual state machine development, but these tools are primarily browser-based and not designed for agent-based workflows in text consoles. This design explores conventions to enable effective XState prototyping with an AI agent assistant.

## Requirements

### Primary Use Case
Quick prototyping and learning XState concepts in a console environment.

### Key Pain Point
**Setup overhead** - Creating package.json, installing dependencies, and configuring TypeScript repeatedly kills rapid prototyping flow.

### Constraints
- XState v5 only (no v4 APIs or documentation)
- Text-based console environment (no GUI access)
- Agent cannot directly access browser-rendered tools (Stately Studio, CodeSandbox)
- TypeScript 5.0+ required for XState v5

## Research Findings

### Existing Stately/XState Tooling

**Stately Studio (Web-based):**
- REST API for programmatic access to projects/machines
- Export to TypeScript, JavaScript, JSON
- Bidirectional GitHub sync
- Visual editor with simulation
- Can export to CodeSandbox for live sharing
- Supports XState v5 import/export

**XState CLI (@xstate/cli):**
- Stately Sky integration for syncing machines from Studio
- Watch mode for file changes
- Note: Typegen is v4 only; v5 uses `setup()` function instead

**Inspection API (v5 built-in):**
- Part of `createActor()` options
- Console-based via `inspect` callback
- Inspection events: `@xstate.actor`, `@xstate.snapshot`, `@xstate.event`, `@xstate.microstep`
- Fully customizable logging/debugging

**Gaps for Agent Workflows:**
- No built-in terminal/console visualization (ASCII diagrams, tree views, etc.)
- No interactive REPL for exploring state machines
- @xstate/inspect package (browser visualizer) is v4 only
- Agent cannot parse browser-rendered content from Stately Studio or CodeSandbox URLs

## Design: Agent-Automated Sandbox Conventions

### Approach
**Agent-automated setup** - Agent follows conventions to create sandbox from scratch each time, providing maximum flexibility for custom configurations.

## Convention 1: Sandbox Location & Lifecycle

### Location Strategy
- **Primary sandbox:** `/tmp/xstate-sandbox/` (ephemeral, auto-cleanup on reboot)
- **Named sandboxes:** `/tmp/xstate-sandbox-{name}/` for experiments to keep temporarily
- **Promotion path:** When prototyping is done, agent helps move code to proper project location

### Lifecycle Management
1. **Initialization:** Agent asks "New sandbox or use existing?" before starting
2. **Validation:** If existing, checks validity and offers to reinstall deps if needed
3. **Cleanup:** Agent offers to remove sandbox when session ends or work is complete

### Rationale for /tmp
- Clear signal that code is throwaway/experimental
- Automatic cleanup on system reboot
- Fast SSD storage on most systems
- No git tracking concerns
- Explicit promotion step when code becomes permanent

## Convention 2: Console Output & Visualization

### Helper Utilities
Agent automatically includes helper utilities in sandbox setup:

**Helper: `helpers/inspect.ts`**
- Formatted console inspector for readable state transitions
- Shows: current state, event received, context changes, next state
- Uses colors/formatting for visual hierarchy (chalk library)
- Example output:
  ```
  → Event: TOGGLE
  ✓ inactive → active
  📊 Context: { count: 1 }
  ```

**Helper: `helpers/runner.ts`**
- Quick test runner for state machines
- Send multiple events in sequence
- Print full execution trace
- Example API: `run(machine, ['TOGGLE', 'TOGGLE', 'RESET'])`

### Agent Conventions
- Always imports and uses these helpers in sandbox examples
- Automatically adds `inspect` option to `createActor()` calls
- Formats output for console readability
- Provides clear visualization of state transitions without GUI

## Convention 3: Package Setup & Dependencies

### Automated Initialization

When creating a new sandbox, agent executes:

**1. Minimal package.json:**
```json
{
  "name": "xstate-sandbox",
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "run": "tsx src/index.ts"
  }
}
```

**2. Core dependencies installed:**
- `xstate` (latest v5)
- `tsx` (fast TypeScript execution, no build step needed)
- `chalk` (for colored console output)
- `typescript` (as dev dependency)

**3. TypeScript configuration (tsconfig.json):**
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strictNullChecks": true,
    "skipLibCheck": true
  }
}
```

### Setup Conventions
- Use `npm install` (fast, widely compatible)
- No build step - use `tsx` for direct TypeScript execution
- Watch mode available via `npm run dev` for live reloading
- Minimal configuration to reduce cognitive overhead

## Convention 4: File Structure & Stately Studio Integration

### Standard Sandbox Structure
```
/tmp/xstate-sandbox/
├── package.json
├── tsconfig.json
├── src/
│   ├── index.ts          # Main file for current experiment
│   ├── machines/         # State machine definitions
│   └── helpers/          # inspect.ts, runner.ts utilities
└── README.md             # Quick reference for sandbox commands
```

### Stately Studio Integration Points

**Export to Studio:**
When machines become complex and visual feedback would help:
- Agent suggests using Stately Studio visualizer
- Explains how to copy machine definition to Studio editor
- Note: No automated export (Studio API requires authentication/projects)

**Import from Studio:**
If designing in Studio first:
- Agent helps sync via GitHub (if repo connected)
- Or accepts copy/paste of machine definition from Studio export
- Agent ensures exported code follows v5 conventions

**CLI Integration:**
Agent can set up `@xstate/cli` for Stately Sky features if using Studio projects.

### Integration Philosophy
- **Local-first workflow** by default (no Studio required)
- Agent suggests Studio for complex visualizations
- No forced integration - Studio is optional enhancement
- Manual copy/paste bridge between agent and browser tools

## Areas Requiring Further Research

### Stately Studio → CodeSandbox Integration
- Stately Studio can export machines to live CodeSandbox environments
- This is a significant integration point for sharing/learning
- Agent cannot directly parse these browser-rendered environments
- Potential workflow: Studio → CodeSandbox → manual copy/paste → agent sandbox
- **Research needed:** Explore CodeSandbox API or other programmatic access methods

### Stately Studio REST API
- API exists for programmatic access to projects/machines
- Requires authentication and project setup
- **Research needed:** Evaluate if API integration would benefit agent workflows
- Consider trade-offs: complexity vs. automation benefits

### XState v5 Ecosystem Maturity
- Some tools still have partial v5 support
- **Research needed:** Monitor ecosystem for new v5-specific tooling
- Stay updated on Stately Studio v5 feature completeness

## Next Steps

1. **Research Phase:**
   - Investigate Stately Studio REST API capabilities
   - Explore CodeSandbox integration options
   - Review XState v5 ecosystem for new tools

2. **Implementation Phase (when ready):**
   - Create helper utilities (inspect.ts, runner.ts)
   - Build agent automation scripts for sandbox initialization
   - Test workflow with real prototyping scenarios
   - Integrate conventions into xstate skill

3. **Iteration:**
   - Gather feedback from actual usage
   - Refine conventions based on friction points
   - Update skill documentation with proven patterns

## Success Criteria

- Agent can create functional XState sandbox in < 30 seconds
- Console output clearly shows state transitions without GUI
- Zero manual setup required for basic prototyping
- Easy promotion path from prototype to production code
- Conventions documented and integrated into xstate skill
