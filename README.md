# XState Skill

> **⚠️ ALPHA STATUS - WORK IN PROGRESS**
>
> This skill is in active development and should be considered alpha-level software. APIs, conventions, and documentation may change significantly. Use at your own risk and expect rough edges.

A Claude Code skill for working with XState v5 - a state management and orchestration library using state machines, statecharts, and the actor model for complex logic in JavaScript/TypeScript applications.

## What is This?

This is a **skill** for [Claude Code](https://claude.com/claude-code) - a modular package that extends Claude's capabilities with specialized knowledge and workflows for XState v5 development.

## Features

- **XState v5 Only**: Comprehensive v5 API coverage with explicit v4 exclusion
- **Quick Reference Examples**: Beginner to advanced state machine patterns
- **Type Safety Guidance**: TypeScript 5.0+ configuration and best practices
- **Actor Model Support**: State machine, promise, callback, observable, and transition actors
- **Debugging Tools**: Inspection API patterns for comprehensive debugging
- **Reference Documentation**: Detailed API docs and patterns in bundled references

## Installation

### For Claude Code Users

1. **Download the skill:**
   ```bash
   # Clone this repository
   git clone https://github.com/justSteve/XState-Skill.git

   # Or download the latest release
   # (releases coming soon)
   ```

2. **Install to your Claude Code skills directory:**
   ```bash
   # Copy to your skills directory
   cp -r XState-Skill ~/.claude/skills/xstate

   # Or create a symlink for easier updates
   ln -s /path/to/XState-Skill ~/.claude/skills/xstate
   ```

3. **Restart Claude Code** to load the skill

4. **Verify installation:**
   The skill should now appear in your available skills list. You can verify by asking Claude: "What skills do you have available?"

### Manual Installation (from ZIP)

If you receive this skill as a `.zip` file:

```bash
# Extract the skill
unzip xstate.zip -d xstate-skill

# Move to skills directory
mv xstate-skill ~/.claude/skills/xstate

# Restart Claude Code
```

## Usage

Once installed, Claude will automatically use this skill when you:

- Work with XState v5 implementations
- Design state machines or statecharts
- Implement actor systems
- Debug state machine behavior
- Migrate from XState v4 to v5
- Integrate XState with React, Vue, Svelte, or other frameworks

### Example Interactions

```
You: "Help me create a toggle state machine with XState"
Claude: [Uses the xstate skill to provide v5-compliant implementation]

You: "How do I use callback actors for event listeners?"
Claude: [References skill examples and provides v5 patterns]

You: "Set up TypeScript for XState v5"
Claude: [Provides proper tsconfig and type safety patterns]
```

## Skill Contents

```
xstate/
├── SKILL.md              # Main skill instructions and examples
├── references/           # Detailed documentation
│   ├── state.md         # Complete API reference
│   └── index.md         # Reference overview
└── docs/
    └── plans/           # Design documents and research
```

## Requirements

- **Claude Code**: This skill requires Claude Code CLI
- **XState v5**: All examples and patterns use XState v5 APIs
- **TypeScript 5.0+**: Required for XState v5 development

## What's Inside

### Quick Reference Examples

- Simple counter machine (beginner)
- Toggle machine with states (beginner)
- Type-safe machines with `setup()` (intermediate)
- Callback actors for event listeners (intermediate)
- Observable actors with RxJS (advanced)
- Inspection API for debugging (advanced)

### Reference Documentation

The `references/` directory contains comprehensive documentation on:
- Observable actors and RxJS integration
- TypeScript setup and type safety
- Callback actors for event handling
- Complete cheatsheet with syntax examples
- Inspection API for debugging
- State machine actors and capabilities
- Actor input/output patterns

## Development Status

This skill is being developed with the following goals:

- [x] Core XState v5 skill documentation
- [x] v5-only warnings and guidance
- [x] Quick reference examples
- [ ] Agent workflow conventions (in design phase)
- [ ] Helper utilities for console output
- [ ] Sandbox automation scripts
- [ ] Working examples and templates
- [ ] Testing and validation

See [docs/plans/](./docs/plans/) for current design work and research.

## Contributing

This is currently a personal/experimental project. If you have suggestions or find issues:

1. Open an issue describing the problem or enhancement
2. For XState-specific questions, refer to [Stately documentation](https://stately.ai/docs/xstate)
3. For skill improvements, describe your use case and desired behavior

## Resources

- **XState v5 Documentation**: https://stately.ai/docs/xstate
- **Stately Studio**: Visual editor for state machines
- **Claude Code**: https://claude.com/claude-code
- **Skill Creation Guide**: See Claude Code documentation

## License

[Add your license here]

## Acknowledgments

- XState and Stately.ai team for the excellent state management library
- Original skill structure from [Skill Seekers](https://github.com/yusufkaraaslan/Skill_Seekers/)
- Claude Code team for the skills framework
