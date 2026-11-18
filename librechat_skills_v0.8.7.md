[Features](/v0.8.7/docs/features)[Skills](/v0.8.7/docs/features/skills)

# Skills

Create reusable SKILL.md instructions that agents can invoke manually, automatically, or on every turn.

Archived documentation

You are reading the frozen documentation for **v0.8.7**, which is no longer maintained. [Go to the latest docs](/docs/features/skills).

Skills are reusable instruction bundles for LibreChat Agents. A skill is centered on a `SKILL.md` file: frontmatter describes when the skill should be used, and the markdown body gives the agent the procedure, rules, examples, or references to follow.

Skills are useful for repeatable work such as:

*   Applying brand or writing guidelines
*   Following internal review checklists
*   Running a standard research workflow
*   Priming a specialized tool workflow
*   Packaging reusable scripts, references, and assets with an instruction file

## [Enable Skills](#enable-skills)

The `skills` agent capability is enabled by default. Admins can remove it from the agents endpoint capability list to hide Skills from users.

```
endpoints:
  agents:
    capabilities:
      - 'deferred_tools'
      - 'execute_code'
      - 'file_search'
      - 'web_search'
      - 'artifacts'
      - 'subagents'
      - 'actions'
      - 'context'
      - 'skills'
      - 'tools'
      - 'chain'
      - 'ocr'
```

Role permissions also control who can use, create, share, and publicly share skills.

## [Deployment Skills](#deployment-skills)

Admins can ship read-only Skills from the filesystem with `DEPLOYMENT_SKILLS_DIR`.

```
DEPLOYMENT_SKILLS_DIR=./skill
```

The directory defaults to `./skill` at the project root. LibreChat loads deployment Skills at startup and exposes them to users with the Skills capability enabled.

Deployment Skills:

*   Are read-only in the UI
*   Use `deployment` as their source
*   Take precedence over persisted Skills with the same name
*   Require a LibreChat restart after files are added, removed, or changed

## [GitHub Skill Sync](#github-skill-sync)

Admins can mirror Skills from GitHub repositories with `skillSync.github` in `librechat.yaml`.

```
skillSync:
  github:
    enabled: true
    intervalMinutes: 60
    runOnStartup: true
    sources:
      - id: librechat-skills
        owner: your-org
        repo: your-skills-repo
        ref: main
        paths:
          - skills
        skillDiscoveryDepth: 2
        token: '${GITHUB_SKILLS_TOKEN}'
```

GitHub Skill Sync:

*   Scans configured repository paths for `SKILL.md`
*   Imports bundled files beside each skill
*   Stores mirrored Skills with `source: "github"`
*   Updates mirrored Skills when the upstream repository changes
*   Removes mirrored Skills that no longer exist in the configured source
*   Supports scheduled, startup, and manual admin-triggered runs

Use a GitHub fine-grained personal access token with read-only Contents and Metadata permissions for the selected repository. See [Skill Sync Object Structure](/v0.8.7/docs/configuration/librechat_yaml/object_structure/skill_sync) for all fields, credential options, tenant scoping, and admin sync endpoints.

## [Create a Skill](#create-a-skill)

Open **Skills** from the side panel. You can write a skill directly in LibreChat or upload a `.md`, `.zip`, or `.skill` file that contains `SKILL.md`.

Minimum `SKILL.md`:

```
---
name: brand-guidelines
description: Use when writing public-facing content that must follow the company voice and terminology.
---

# Brand Guidelines

Write in a concise, practical tone.
Prefer active voice.
Use product terminology consistently.
```

### [Frontmatter](#frontmatter)

| Key                      | Type                  | Description                                                                                                                                   | Example                                                  |
| ------------------------ | --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| name                     | String                | Stable kebab-case identifier. It must start with a lowercase letter or digit and can contain lowercase letters, digits, and hyphens.          | name: brand-guidelines                                   |
| description              | String                | The most important trigger text. Describe when the model should use the skill.                                                                | description: Use when writing public-facing launch copy. |
| always-apply             | Boolean               | Automatically primes the skill into every turn where it is active.                                                                            | always-apply: true                                       |
| user-invocable           | Boolean               | Set to false to hide the skill from manual \`$\` invocation. Default: true.                                                                   | user-invocable: false                                    |
| disable-model-invocation | Boolean               | Set to true to exclude the skill from the model-invoked skill catalog. Manual invocation is still allowed unless \`user-invocable\` is false. | disable-model-invocation: true                           |
| allowed-tools            | Array/List of Strings | Temporarily unions these tools into the agent effective tool set when the skill is manually or always applied.                                | allowed-tools: \["execute\_code"\]                       |

## [Invocation Modes](#invocation-modes)

Skills can reach an agent in three ways:

*   **Manual**: the user types `$` in chat and selects a skill from the popover.
*   **Model-invoked**: the model chooses a skill from the injected skill catalog and calls the skill tool.
*   **Always apply**: the skill is primed into every turn when active.

Manual invocation is explicit user intent. It can use skills that are hidden from model invocation with `disable-model-invocation: true`, as long as `user-invocable` is not false.

## [Agent Scope](#agent-scope)

Agents must have Skills enabled before they can use the catalog.

*   If `skills_enabled` is false or unset on a persisted agent, skills are inactive for that agent.
*   If `skills_enabled` is true and no skill allowlist is set, the agent can use the full active catalog visible to the user.
*   If `skills_enabled` is true and `skills` contains specific skill IDs, the agent is narrowed to that list.

This lets admins expose the Skills feature globally while keeping each agent's usable skill set focused.

## [Active and Shared Skills](#active-and-shared-skills)

Users can toggle skills active or inactive. Owned skills default to active. Shared skills use the admin-configured default until the user overrides them.

Inactive skills are excluded from:

*   The `$` popover
*   The model-invoked skill catalog
*   Always-apply priming

## [Bundled Files](#bundled-files)

Uploaded skill bundles can include files alongside `SKILL.md`, such as:

*   `references/...`
*   `scripts/...`
*   `assets/...`

The backend stores those files with the skill. The agent can resolve skill files when the skill is active and in scope.

Authoring tip

Keep `description` specific. It is the strongest signal for model-invoked skills. A short or vague description will under-trigger.

[Agents

Learn how to create, customize, and leverage LibreChat's AI Agents - a powerful framework for building custom AI assistants with any model provider.](/v0.8.7/docs/features/agents)

[Subagents

Delegate focused work from one agent to isolated child agent runs.](/v0.8.7/docs/features/subagents)

### On this page

[Enable Skills](#enable-skills)[Deployment Skills](#deployment-skills)[GitHub Skill Sync](#github-skill-sync)[Create a Skill](#create-a-skill)[Frontmatter](#frontmatter)[Invocation Modes](#invocation-modes)[Agent Scope](#agent-scope)[Active and Shared Skills](#active-and-shared-skills)[Bundled Files](#bundled-files)
