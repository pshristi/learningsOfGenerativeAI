# Learnings of Generative AI

My compiled learning notes from courses on Generative AI.

---

## Table of Contents

- [1. Generative AI for Everyone](#1-generative-ai-for-everyone)
  - [Introduction to GenAI](#introduction-to-genai)
  - [How GenAI Works](#how-genai-works)
  - [Limitations](#limitations)
  - [Tips for Prompting](#tips-for-prompting)
  - [Tools to Improve Performance](#tools-to-improve-performance)
  - [Advanced Technologies: Beyond Prompting](#advanced-technologies-beyond-prompting)
  - [How LLMs Follow Instructions](#how-llms-follow-instructions)
- [2. Agentic AI](#2-agentic-ai)
  - [Introduction to Agentic Workflows](#introduction-to-agentic-workflows)
  - [Key Benefits of Agentic Workflows](#key-benefits-of-agentic-workflows)
  - [What Tasks Is Agentic AI Suited For?](#what-tasks-is-agentic-ai-suited-for)
  - [Building Blocks](#building-blocks)
  - [Evaluating Agentic AI](#evaluating-agentic-ai)
  - [Agentic Design Patterns](#agentic-design-patterns)
    - [Reflection](#reflection)
    - [Tool Use](#tool-use)
    - [Planning](#planning)
    - [Multi-Agent Collaboration](#multi-agent-collaboration)
  - [Evaluations (Evals)](#evaluations-evals)
  - [Error Analysis and Prioritizing Next Steps](#error-analysis-and-prioritizing-next-steps)
  - [Improving Performance](#improving-performance)
  - [Development Process Summary](#development-process-summary)
- [3. Agent Skills with Anthropic](#3-agent-skills-with-anthropic)
  - [What Are Agent Skills?](#what-are-agent-skills)
  - [The New Paradigm](#the-new-paradigm)
  - [What Skills Enable](#what-skills-enable)
  - [Key Characteristics of Skills](#key-characteristics-of-skills)
  - [How Do Skills Work? - Progressive Disclosure](#how-do-skills-work---progressive-disclosure)
  - [Skills vs Tools, MCP, and Subagents](#skills-vs-tools-mcp-and-subagents)
  - [SKILL.md File Structure](#skillmd-file-structure)
  - [Practical Guidance](#practical-guidance)
  - [Evaluation](#evaluation)
  - [Skills and Slash Commands Have Been Merged](#skills-and-slash-commands-have-been-merged)
  - [Subagents and Skills](#subagents-and-skills)
- [4. Spec-Driven Development with Coding Agents](#4-spec-driven-development-with-coding-agents)
  - [Core Idea](#core-idea)
  - [Why SDD](#why-sdd)
  - [Workflow Overview](#workflow-overview)
  - [The Constitution](#the-constitution)
  - [Feature Loop](#feature-loop)
  - [Replanning](#replanning)
  - [MVP](#mvp)
  - [Legacy / Brownfield Projects](#legacy--brownfield-projects)
  - [Custom Workflows / Skills](#custom-workflows--skills)
  - [Agent Replaceability](#agent-replaceability)
  - [SDD Workflow at a Glance](#sdd-workflow-at-a-glance)
- [Acknowledgement](#acknowledgement)
- [Connect](#connect)

---

## 1. Generative AI for Everyone

### Introduction to GenAI

### How GenAI Works

- LLMs are built by using supervised learning to repeatedly predict next word.
- When we train a very large AI system on a lot of data (hundreds of billions of words) we get a Large Language Model like ChatGPT.

### Limitations

- No memory of previous tasks completed
- Knowledge cutoffs
- Making things up: Hallucinations
- The input (and output) length is limited
- Generative AI does not work well with structured (tabular) data but with unstructured data
- Bias and Toxicity

### Tips for Prompting

- Be detailed and specific
- Guide the model to think through its answer
- Experiment and iterate

> Image generation works on diffusion model

### Tools to Improve Performance

We repeatedly find and fix mistakes.

| Technique | Description |
|-----------|-------------|
| Prompting | Craft effective prompts |
| Retrieval Augmented Generation (RAG) | Give LLM access to external data sources |
| Fine-tune models | Adapt LLM to your task |
| Pretrain models | Train LLM from scratch |

**What is a token?**
Roughly, 1 token = 3/4 words

### Advanced Technologies: Beyond Prompting

**RAG:**

Big Idea: **LLM as reasoning engine**

- LLMs have a lot of general knowledge, but they don't know everything
- By providing relevant context in the prompt, we ask an LLM to read a piece of text, then process it to get an answer
- We're using it as a reasoning engine to process information, rather than using it as a source of information

**Fine-tuning:**

- To carry out a task that isn't easy to define in a prompt.

**Pretraining an LLM:**

- For building a specific application, Option of last resort. Could help if have a highly specialized domain

**Choosing a model:**

- Based on model size
- Closed or open source

### How LLMs Follow Instructions

- They are fine tuned based on instructions data
- Reinforcement learning from human feedback (RLHF)
- Helpful, Honest, Harmless
  - **Step 1:** Train an answer quality (reward) model
  - **Step 2:** Have LLM generate a lot of answers. Further train it to generate more responses that get high scores.

---

## 2. Agentic AI

### Introduction to Agentic Workflows

- **Non-agentic workflow:** zero shot
- An agentic AI workflow is a process where an LLM-based app executes multiple steps to complete a task.

**Degree of Agenticness:** Agentic AI can be less or more autonomous

| Level | Characteristics |
|-------|----------------|
| **Less autonomous** | All steps are predetermined; All tools use hard coded; Autonomy is in text generation |
| **Semi autonomous** | Agent can make some decisions and determine tools to use; All tools predefined |
| **Fully autonomous** | Agents make many decisions autonomously; Can make tools on the fly |

### Key Benefits of Agentic Workflows

- Much better performance
- Faster than humans due to parallelization
- Modular: can add or swap out tools, models

### What Tasks Is Agentic AI Suited For?

**Easier when:**
- Clear step by step process
- Standard procedures to follow
- Text assets only

**Harder when:**
- Steps not known ahead of time
- Plan/solve as you go
- Multimodal

### Building Blocks

**Models:**
- LLMs (Text generation, tool use, information extraction)
- Other models (PDF-to-text, text-to-speech, image analysis)

**Tools:**
- API (Web search, get real-time data, send email, check calendar)
- Information retrieval (RAG, DB)
- Code execution (Basic calculator, data analysis)

### Evaluating Agentic AI

- Manually look for low quality outputs and add evaluation to track such errors. For objective error: use code for occurrences
- Use LLM as a judge for subjective error
- Check for end-to-end eval as well as component level eval
- Examine traces to perform error analysis

### Agentic Design Patterns

#### Reflection

- Ask LLM to iterate over its outputs using LLM, tools, human feedback to improve output iteratively
- Mostly use reasoning model for reflection
- Use reflection prompt such that it clearly indicates the reflection action and specifies criteria to check

**Evaluating Reflection:**

- **Objective evals**
  - Build a dataset of ground truth examples
  - Code-based evals
- **Subjective evals**
  - Use LLM as a judge
  - Rubric-based grading is better
  - **Rubric:** define set of criterias and ask LLM to score as binary against those criterias and add the result to get final output score.

> It is seen that reflection tends to perform better than one shot, which then further be improved using external feedback (using tools, function etc)

#### Tool Use

Tools are just code/api/function that the LLM can request to be executed.

**Earlier approach:**
1. First, you have to provide the tool to the LLM — implement the function, and then tell the LLM that it is available.
2. When the LLM decides to call a tool, it then generates a specific output that lets you know that you need to call the function for the LLM.
3. Then you call the function, get its output, take the output of the function you just called, and give that output back to the LLM, and the LLM then uses that to go on to whatever it decides to do next.

**Example — System prompt:**

> You have access to a tool called get_current_time. To use it, return the following exactly:
> FUNCTION:
> get_current_time()

This will be converted to a predefined JSON schema:

```json
tools = [{
  "type": "function",
  "function": {
    "name": "get_current_time",
    "description": "Returns current time for the given timezone.",
    "parameters": {
      "timezone": {
        "type": "string",
        "description": "The IANA time zone string, e.g., 'America/New_York' or 'Pacific/Auckland'."
      }
    }
  }
}]
```

- In agentic AI, LLM can decide on tool use when appropriate from available tool list

**Tool Use via Code Execution:**

- Instead of creating tool for each action, we can ask LLM to generate code to achieve what is expected from tool, and then execute that code and return result to LLM.

**MCP (Model Context Protocol):**

- The Model Context Protocol was a standard proposed by Anthropic but now adopted by many other companies and by many developers as a way to give an LLM access to more context and to more tools.
- **MCP clients:** These are the applications that request access to tools or to data. Eg. Cursor, Claude Desktop, Windsurf
- **MCP servers:** Are the software wrappers that give access to data or take actions at different types of resources. Eg. Slack, GitHub, Postgres MCP server

#### Planning

Rather than defining steps to take, let LLM determine those.

**Example — System prompt:**

> You have access to the following tools:
> {description of tools}
> Return a step-by-step plan to carry out the user's request

**Formatting plan as JSON:**

> You have access to the following tools: {description of tools}
> Create a step-by-step plan in JSON format. Each step should have the following items: step number, description, tool name, and args.

Then asking LLM to execute this plan step by step.

**Planning with Code Execution:**

For complex workflows and variety of tasks, it becomes the challenge of planning with tools.

> Write code to solve the user's query. Return your answer as python code delimited with `<execute_python>` and `</execute_python>` tags.

> In many tasks, it was observed that as compared to "Text as Action" and "JSON as Action", **"Code as Action"** (Planning with code) improves performance.

#### Multi-Agent Collaboration

**Communication patterns for multi-agent systems:**

- **Linear workflow:** plan of agents
- **Hierarchical workflow:** a manager agent acts as an orchestrator
- **Deeper hierarchy:** each agent can perform certain task along with using other agents to achieve that task
- **All-to-all:** each agent talks to other

### Evaluations (Evals)

- Build a system and look at outputs to discover where it is behaving in an unsatisfactory way
- Drive improvement by putting in place a small eval with ~20 examples to help you track progress
- Monitor as you make changes to workflow (e.g. new prompts, new algorithms) and see if the metric improves

**Two "axes" of evaluation:**

|  | Per example ground truth | No per example ground truth |
|--|--------------------------|----------------------------|
| **Evaluate with code (objective)** | Eg: Checking invoice date extraction — `if (extracted_date == actual_date): num_correct += 1` | Eg: Checking marketing copy length — `if len(text) <= 10: num_correct += 1` |
| **LLM-as-judge (subjective)** | Eg: Counting gold-standard talking points — "Count the number of gold standard points in the following text…" | Eg: Grading charts with a rubric — "Grade this chart according to (i) whether it has clear axes labels, (ii) …." |

**Tips for designing end-to-end evals:**

- Quick and dirty is ok to start!
- As you find places where your evals fail to capture human judgement as to what system is better, use that as an opportunity to improve the metric
- Look for places where performance is worse than humans

### Error Analysis and Prioritizing Next Steps

- Examine traces and spans to better understand each step in the workflow
- Carry out error analysis to figure out what component performed poorly, leading to a poor final output
- Use error analysis output to decide where to focus efforts
- To carry out error analysis, focus on examples where performance is subpar

**End-to-end eval is expensive, and hence component-level evaluations help:**
- Can provide clearer signal for specific errors
- Avoid the noise in end-to-end system
- More efficient for focused team to optimize
- Work on smaller, more targeted problems faster

### Improving Performance

**Improving non-LLM component performance:**
- Tune hyperparameters of component
- Replace the component

**Improving LLM component performance:**
- Improve your prompts
- Add more explicit instructions
- Add one or more concrete examples to the prompt (few-shot prompting)
- Try a new model
- Split up the step: Decompose the task into smaller steps
- Fine-tune a model

**Developing intuition for model intelligence:**
- Play with models often
- Read other people's prompts for ideas of how to best use models
- Use different models in your agentic workflows to know which models work for which types of tasks

**Latency, cost optimization:**
- Consider parallelism

### Development Process Summary

Navigate between **Build** and **Analyse:**

| Build | Analyse |
|-------|---------|
| Build end-to-end system | Examine outputs; traces |
| Improve individual component | Build evals; compute metrics |
| | Error analysis |
| | Component-level evals |

---

## 3. Agent Skills with Anthropic

### What Are Agent Skills?

Agent Skills are a lightweight, open format for extending AI agent capabilities. A skill is a folder of organized files consisting of instructions, scripts, assets, and resources that agents can discover to perform specific tasks accurately.

### The New Paradigm

**How We Used to Think About Agents:**
- Each with their own narrow focus, their own scaffolding, and specific tools

**The New Paradigm:**
- Skills provide procedural knowledge and company/team/user-specific context that agents can load on demand

### What Skills Enable

- Domain Expertise
- Repeatable Workflows
- New Capabilities

**Without Skills:**
- Describe your instructions and requirements every time
- Bundle all your references and supporting files every time
- Manually ensure the workflow or outputs are always consistent

### Key Characteristics of Skills

- **Portable:** You can reuse the same skill across different skills-compatible agents
- **Composable:** Skills can be combined to build complex workflows

### How Do Skills Work? - Progressive Disclosure

Skills can contain a lot of information, and you may have hundreds of them. To protect the context window, skills are progressively disclosed:

| Layer | What | When Loaded |
|-------|------|-------------|
| Metadata | YAML frontmatter: name, description | Always loaded |
| Instructions | Main SKILL.md content | Loaded when triggered |
| Resources | Reference files, scripts | Loaded as needed |

### Skills vs Tools, MCP, and Subagents

**Skill Structure Examples:**
According to the Agent Skills Specification:
- `SKILL.md` is required
- Optional directories: `references`, `scripts`, and `assets`

**Skills vs MCP:**
- **MCP:** Connects your agent to external systems and data (databases, APIs, services)
- **Skills:** Teaches your agent what to do with that data
- MCP provides access, Skills provide expertise.

**Skills vs Tools:**

| Tools | Skills |
|-------|--------|
| Provide agents with essential capabilities to accomplish tasks | Extend agent's capabilities with specialized knowledge |
| Tool definitions (name, description, parameters) always live in the context window | Skills are loaded dynamically as needed |
| Fixed set of capabilities | Skills can include scripts as tools that are used when needed ("tools on demand") |

**Skills vs Subagents:**

| Subagents | Skills |
|-----------|--------|
| Have their own isolated context and tool permissions | Provide expertise knowledge to the main agent or any of its subagents |
| Agent delegates a task to the specialized subagent, which works independently (and maybe in parallel) and returns results | Skills inform how work should be done |
| | Skills can enhance both the main agent AND its subagents with specialized knowledge |

### SKILL.md File Structure

A skill file has two main parts:
- **YAML Frontmatter:** Metadata at the top
- **Body Content:** Markdown instructions below

**Frontmatter Required Fields:**
- **name:** Max 64 chars; lowercase letters, numbers, and hyphens only; must not start/end with hyphens; must match parent directory name; recommended: gerund (verb+-ing) form
- **description:** Max 1024 chars; non-empty; should describe what the skill does AND when to use it; include specific keywords to help agents identify relevant tasks

**Frontmatter Optional Fields:**
- `license`, `compatibility`, `metadata`, `allowed-tools`

### Practical Guidance

**Body Content:** No format restrictions, but here are some recommendations.

**Recommended Sections:**
- Step-by-step instructions
- Input Format / Output Format / Examples
- Common edge cases

**Best Practices:**
- Keep it under 500 lines
- Move detailed reference material to separate files (show basic content, link to advanced content)
- Keep references one level deep from SKILL.md (avoid nested file references)
- Be clear and concise, use consistent terminology
- Use forward slashes in file paths, even on Windows

**Degrees of Freedom:**

| Level | Description |
|-------|-------------|
| High freedom | General text-based directions; multiple approaches are valid |
| Medium freedom | Instructions contain customizable pseudocode, code examples, or patterns; a preferred pattern exists but some variation is acceptable |
| Low freedom | Instructions refer to specific scripts; a specific sequence must be followed |

**Complex Workflows:**
- Break complex operations into clear, sequential steps
- If workflows become large with many steps, consider pushing them into separate files

**Optional Directories:**
- `/assets`
- `/references`
- `/scripts`

### Evaluation

**Unit Tests:** Define test cases with:
- `skills`: Which skills to test
- `queries`: Test prompts to run
- `files`: Input files to use
- `expected_behavior`: What success looks like

**Additional Evaluation Tips:**
- Get human feedback
- Test with all the models you plan to use

### Skills and Slash Commands Have Been Merged

Before Skills were introduced, we had a feature called slash commands. This feature allows you to create custom commands by describing them in a markdown file saved in a commands.

As of January 23, custom slash commands have been merged into skills. This merger occurred because a file at `/commands/review.md` and a skill at `/skills/review/SKILL.md` both create the `/review` command and work the same way. However, skills offer additional optional features: a dedicated directory for supporting files that can be referenced in the SKILL.md, and frontmatter options to control whether you or LLM invokes them.

### Subagents and Skills

When you create a subagent, you can use the `skills` field to inject skill content into the subagent's context at startup. Since subagents don't inherit skills from the parent conversation, you must list the skills explicitly. The full content of the SKILL.md file is injected into the subagent's context when the subagent is invoked.

If you want a specific subagent (built-in or custom) to be used with the skill, you need to specify the `agent` field in the skill's frontmatter.

**References:**
- [agentskills.io](https://agentskills.io/home#adoption)
- [Course Files (GitHub)](https://github.com/https-deeplearning-ai/sc-agent-skills-files)

---

## 4. Spec-Driven Development with Coding Agents

### Core Idea

Instead of vibe coding (prompt -> hope -> fix -> repeat), you write a **specification** first. The spec is the "brain"; the agent is the "muscle."

### Why SDD

Vibe coding doesn't scale. Specs create a persistent artifact that survives context window resets (Specs persist between sessions and even agents), reduces cognitive overhead by being able to control large code changes with small changes to the spec, and improves how accurately the agent matches your intent.

Professional response to the chaos of unsupervised AI generation.

With specs, we get a contract between the humans, but also with the agent. Your main task as the human now shifts. **Learn how to convert your intentions into clear specifications.**

### Workflow Overview

Three layers:
- **Constitution:** project-level
- **Feature specs:** feature-level
- **Replanning:** between features

Analogy: you're the architect with blueprints; the agent is the builder.

Specs are part of code in your project version history.

### The Constitution

- What really is your project? What's the core idea behind its development?
- How does it fit within your company's preferred tech stack?
- What features are planned?

SDD can work for both a **Greenfield project** (start from scratch) or **Brownfield project** (existing codebase).

Three files written with the agent in conversation:
- **`mission.md`** — why + who
- **`tech-stack.md`** — how it's built
- **`roadmap.md`** — what and in what order

### Feature Loop

For each feature: **Plan -> Implement -> Validate**

- Each step happens in a fresh agent context with human-in-loop
- Work in its own Git branch
- Spec files are committed alongside code

**Feature Specification:**
- For each feature in roadmap, referring mission and tech-stack
- A new directory with feature name is created under specs for this feature work
- In there:
  - `plan.md` as a series of numbered task groups
  - `requirements.md` for the scope, decisions, context
  - `validation.md` for how to know the implementation succeeded and can be merged

**Feature Implementation:**
- We collaborate with Agent to implement the task groups

**Feature Validation:**
- We collaborate with Agent to review the work. Any changes needed or manually done should be communicated to Agent to capture impact and change in affected areas
- On Done, mark this `specs/roadmap.md` phase as complete, commit this work, switch to main, and merge this branch, then delete it.
- Do a deep review: Spawn multiple subagents to go through all the changes on this branch from three different perspectives and see if anything doesn't make sense, could be better, etc.

### Replanning

Between features, update the constitution and roadmap. Catch drift before it compounds.

- Small scope changes -> do it now
- Large scope -> schedule on roadmap

### MVP

Once your constitution and early feature specs are solid, you can tell the agent to implement the rest of the roadmap at once. Treat it as a stress test of your specs.

- Ask for the whole plan implementation while referring to the constitution.
- Based on the MVP, reflect to find anything that needs clarification in the specs?

### Legacy / Brownfield Projects

Same process — the agent reverse-engineers a constitution from existing code, READMEs, and TODOs. Then continue with the normal feature loop.

### Custom Workflows / Skills

Automate repetitive SDD prompts using Agent Skills (a standardized format). Skills can be project-scoped or global. MCP servers are being partly replaced by Skills + CLI tools.

- Eg: GitHub's Spec Kit is one attempt at formalizing a spec-driven development workflow with agents.
- Another popular alternative is OpenSpec from Fission AI.

### Agent Replaceability

By following open standards (AGENTS.md, Agent Skills, MCP, ACP), your workflow isn't locked to one agent or IDE.

**Reference project:** [Course Files (GitHub)](https://github.com/https-deeplearning-ai/sc-spec-driven-development-files)

### SDD Workflow at a Glance

```
Constitution (once)
├── mission.md
├── tech-stack.md
└── roadmap.md

Feature loop (repeating)
├── Plan → feature spec (plan + requirements + validation criteria)
├── Implement → agent builds in a branch
└── Validate → human reviews, tests, commits, merges

Replanning (between features)
├── Update constitution if needed
├── Revise roadmap
└── Improve your workflow (skills, automation)
```

---

## Acknowledgement

These notes are compiled from courses offered by [DeepLearning.AI](https://www.deeplearning.ai/). A huge thanks to the DeepLearning.AI team for creating such valuable and accessible learning resources on Generative AI.

---

## Connect

Maintained by **Shristi Pathak**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/shristi-pathak/)
[![Gmail](https://img.shields.io/badge/Gmail-EA4335?style=for-the-badge&logo=gmail&logoColor=white)](mailto:pshristi2006@gmail.com)
