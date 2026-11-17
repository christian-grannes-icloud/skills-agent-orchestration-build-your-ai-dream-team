# Agent team

For Mona's Project Pulse dashboard, I am using a four-agent custom team orchestrated through GitHub Copilot CLI in a Codespace. The definitions live under the repository's `.github/agents/` folder and are assigned to specific roles in the build flow.

- Orchestrator — Model: Claude Opus 5.5 (copilot). Responsible for coordinating the work, breaking the dashboard build into phases, assigning file scope to each specialist, keeping tasks aligned, and validating that the final result fits together as a cohesive Project Pulse experience. File: `.github/agents/orchestrator.agent.md`.
- Planner — Model: Claude Opus 5.5 (copilot). Responsible for researching the repository, reviewing relevant tools and dependencies, identifying edge cases, and producing the ordered implementation plan for the dashboard. File: `.github/agents/planner.agent.md`.
- Coder — Model: GPT-5.5 (copilot). Responsible for implementation, code fixes, logic, and validation within the assigned files, including app support files when the task requires a runnable preview. File: `.github/agents/coder.agent.md`.
- Designer — Model: Gemini 3.7 Flash (copilot). Responsible for the dashboard UI/UX, accessibility, visual hierarchy, spacing, interaction patterns, and overall polish so the project reads like a clear Project Pulse frontend. File: `.github/agents/designer.agent.md`.

How the team will work together to build Project Pulse:

1. The Planner researches the repo and defines the implementation steps, risks, dependencies, and validation expectations.
2. The Orchestrator converts that plan into phases and assigns scoped work to the right specialist agent.
3. The Designer shapes the product look and user experience, focusing on the dashboard structure, readability, and responsive design.
4. The Coder implements the logic and supporting files required to make the interface runnable and functional.
5. The Orchestrator verifies the pieces fit together and checks that the integrated dashboard meets the project goals before completion.

This setup uses GitHub Copilot CLI in a Codespace to coordinate the full custom agent team as they collaborate on the Project Pulse dashboard.
