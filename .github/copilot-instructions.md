===========
In Agent Mode, use the following prompt:

Analyze this codebase to generate or update .github/copilot-instructions.md for guiding AI coding agents.

Focus on discovering the essential knowledge that would help an AI agents be immediately productive in this codebase. Consider aspects like:

The "big picture" architecture that requires reading multiple files to understand - major components, service boundaries, data flows, and the "why" behind structural decisions
Critical developer workflows (builds, tests, debugging) especially commands that aren't obvious from file inspection alone
Project-specific conventions and patterns that differ from common practices
Integration points, external dependencies, and cross-component communication patterns
Source existing AI conventions from **/{.github/copilot-instructions.md,AGENT.md,AGENTS.md,CLAUDE.md,.cursorrules,.windsurfrules,.clinerules,.cursor/rules/**,.windsurf/rules/**,.clinerules/**,README.md} (do one glob search).

Guidelines (read more at https://aka.ms/vscode-instructions-docs):

If .github/copilot-instructions.md exists, merge intelligently - preserve valuable content while updating outdated sections
Write concise, actionable instructions (~20-50 lines) using markdown structure
Include specific examples from the codebase when describing patterns
Avoid generic advice ("write tests", "handle errors") - focus on THIS project's specific approaches
Document only discoverable patterns, not aspirational practices
Reference key files/directories that exemplify important patterns
Update .github/copilot-instructions.md for the user, then ask for feedback on any unclear or incomplete sections to iterate.
===========

# Copilot Instructions for AI Hedge Fund Project

## Project Overview

- **Purpose:** This project implements an AI-driven hedge fund simulation platform. It orchestrates multiple agent-based investment strategies, risk management, and portfolio management using LLMs and financial data.
- **Major Components:**
  - `src/agents/`: Investment advisor agents (e.g., Warren Buffett, Cathie Wood), risk manager, and portfolio manager. Each agent encapsulates a distinct investment style or function.
  - `src/graph/`: State management and agent workflow orchestration using a directed graph (see `main.py`).
  - `src/llm/`: LLM model selection, configuration, and metadata.
  - `src/utils/`: Utilities for analysts, display, progress, API keys, and visualization.
  - `src/tools/`: API integrations for data retrieval.
  - `src/data/`: Data models and caching.

## Key Workflows

- **Run the Application:**
  - Use `./run.sh --run --tickers AAPL,MSFT` (plus options) to start the main simulation. This loads `.env` variables and invokes `src.main`.
  - Advisors and LLM models are selected interactively at runtime.
- **Testing:**
  - `./run.sh --tests` is a placeholder; implement test logic as needed.
- **Shell Access:**
  - `./run.sh --shell` launches an IPython shell with environment setup.
- **Docker:**
  - Build and run using the provided `docker/Dockerfile`. The app runs as a non-root user in `/var/app`.

## Patterns & Conventions

- **Agent Pattern:** Each agent (analyst, risk, portfolio) is a function taking a shared `AgentState` dict. Add new agents in `src/agents/` and register in `src/utils/analysts.py`.
- **Workflow Graph:** Agent execution order is defined dynamically in `main.py` using `StateGraph`. Advisors are user-selected; risk and portfolio managers are always included.
- **Environment:** All runs require a valid `.env` file with at least `OPENAI_API_KEY`.
- **LLM Models:** Models and providers are defined in `src/llm/models.py` and selectable at runtime.
- **Data Flow:**
  - `main.py` orchestrates: parses CLI, builds agent graph, initializes portfolio, and runs the workflow.
  - Agents communicate via the `AgentState` dict, passing data and signals.

## Integration Points

- **External APIs:**
  - Financial data is fetched via `src/tools/api.py` (see `risk_manager.py`).
  - LLMs are accessed via LangChain and OpenAI/other providers.
- **Visualization:**
  - Agent workflow graphs can be saved as PNGs using `src/utils/visualize.py`.

## Examples

- To add a new analyst agent:
  1. Implement in `src/agents/`.
  2. Register in `src/utils/analysts.py` with display name, description, and order.
- To run with custom tickers and cash: `./run.sh --run --tickers TSLA,NVDA --initial-cash 50000`

## Special Notes

- All code is Python 3.12+.
- The project uses `pip-compile` for dependency management (`requirements.in` → `requirements.txt`).
- The app expects to run from `/var/app` (see Docker and `run.sh`).

---

For further conventions or questions, review `src/main.py`, `run.sh`, and agent modules. Update this file as the project evolves.
