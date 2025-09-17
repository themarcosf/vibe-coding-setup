In Agent Mode, start by having sort of a conversation with the AI where you discuss the project, architecture, goals, etc. Prompt it to ask you follow-up questions about the project to clarify its understanding.

After this initial conversation, use the following prompt:

```text
Based on our discussion, analyze this codebase to generate or update .github/copilot-instructions.md for guiding AI coding agents.

Focus on discovering the essential knowledge that would help an AI agent be immediately productive in this codebase. Consider aspects like:

- the "big picture" architecture that requires reading multiple files to understand - major components, service boundaries, data flows, and the "why" behind structural decisions
- critical developer workflows (builds, tests, debugging) especially commands that aren't obvious from file inspection alone
- project-specific conventions and patterns that differ from common practices
- integration points, external dependencies, and cross-component communication patterns
- source existing AI conventions from **/{.github/copilot-instructions.md,AGENT.md,AGENTS.md,CLAUDE.md,.cursorrules,.windsurfrules,.clinerules,.cursor/rules/**,.windsurf/rules/**,.clinerules/**,README.md} (do one glob search).

Guidelines:

- if .github/copilot-instructions.md exists, merge intelligently - preserve valuable content while updating outdated sections
- Write concise, actionable instructions (~20-50 lines) using markdown structure
- Include specific examples from the codebase when describing patterns
- Avoid generic advice ("write tests", "handle errors") - focus on THIS project's specific approaches
- Document only discoverable patterns, not aspirational practices
- Reference key files/directories that exemplify important patterns
Update .github/copilot-instructions.md for the user, then ask for feedback on any unclear or incomplete sections to iterate.
```

All files in .github/instructions/ are local instructions targets at specific filetypes.