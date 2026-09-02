# Agent team

Mona's Project Pulse dashboard is built using a team of four specialized agents coordinated through GitHub Copilot CLI in a Codespace.

## Orchestrator
- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Breaks down complex requests into tasks and delegates to specialist subagents. Coordinates work across Planner, Coder, and Designer agents, ensuring parallel work runs safely and sequential dependencies are honored.
- **Definition:** `.github/agents/orchestrator.agent.md`

## Planner 
- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Creates implementation strategies and technical plans by researching the codebase, documentation, dependencies, and edge cases. Produces actionable plans with file assignments, dependencies, and validation expectations.
- **Definition:** `.github/agents/planner.agent.md`

## Coder
- **Model:** GPT-5.5 (copilot)
- **Responsibility:** Implements code-oriented tasks with clear structure, explicit errors, and testable behavior. Writes code, fixes bugs, and creates support configurations like `.vscode/launch.json` for the runnable dashboard application.
- **Definition:** `.github/agents/coder.agent.md`

## Designer
- **Model:** Gemini 3.1 Pro (copilot)
- **Responsibility:** Handles UI/UX, accessibility, information architecture, and visual design. Creates a polished dashboard with visible project cards, status badges, responsive layout, and clear typography to deliver the best user experience.
- **Definition:** `.github/agents/designer.agent.md`

## Execution model

This team uses GitHub Copilot CLI running in a Codespace to orchestrate the project. The Orchestrator coordinates the work:
1. The Planner researches and creates the implementation strategy
2. Tasks are delegated to Coder and Designer with explicit file scopes
3. Parallel work runs when file scopes don't overlap
4. Sequential work follows dependencies and data flow
5. The integrated result is verified before completion
