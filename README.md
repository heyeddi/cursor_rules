# Cursor Rules - Virtual Team System

A comprehensive cursor rules system that creates a "virtual team" of expert personas to help with development tasks. This system uses a CEO orchestrator to intelligently route requests to the appropriate specialist persona.

## Overview

This system provides a structured approach to AI-assisted development by:
- **Orchestrating** requests through a CEO persona that routes to specialists
- **Specializing** with 14 expert personas covering management, technical, and review roles
- **Planning** with a mandatory workflow that ensures proper planning before execution
- **Standardizing** with universal project conventions that work across projects

## Architecture

### Always-Active Rules (Loaded First)

1. **`workflow.mdc`** - Pre-task planning and role assignment process
2. **`project-standards.mdc`** - Universal project conventions and standards
3. **`ceo.mdc`** - Virtual CEO orchestrator that routes to specialist personas

### Specialist Personas (Smart-Loaded)

#### Management & Strategy Personas
- **Product Manager** - User stories, problem definition, acceptance criteria
- **Software Architect** - System design, architecture, technical decisions
- **CFO** - Financial analysis, ROI, cost-benefit
- **VP of Growth** - Go-to-market strategy, user acquisition, monetization
- **COO** - Operational scaling, internal processes, execution readiness
- **VC Partner** - High-level strategy, competitive moats, market defensibility

#### Technical Execution Personas
- **JAMStack Developer** - Writing code, implementing features (FastAPI + Vue/Hugo)
- **Code Reviewer** - Code quality, maintainability, best practices review
- **QA Engineer** - Test planning, writing tests, quality assurance
- **Security Analyst** - Security reviews, vulnerability assessment
- **UI/UX Designer** - UI/UX feedback, accessibility, usability improvements
- **Technical Writer** - Documentation, API docs, code comments
- **DevOps Engineer** - Deployment, CI/CD, Dockerfiles, infrastructure
- **Git Workflow Specialist** - Git commits, branch management, commit messages

#### Meta Rules
- **Learn** - Self-learning system for capturing workflows and preferences

## How It Works

### 1. Workflow Process
Every task follows a mandatory planning process:
1. Create/update `.todo.md` with plan
2. Read `learnings.md` (if exists) to understand learned rules and preferences
3. Ask clarifying questions
4. Create task checklist with persona assignments
5. Get approval before execution
6. Execute and update checklist

### 2. CEO Orchestration
The CEO persona analyzes each request and routes to the appropriate specialist:
- Reads the request
- Selects the best persona for the task
- States which "hat" it's wearing
- Answers from that persona's perspective

### 3. Persona Activation
Personas are activated based on:
- **USE WHEN** triggers in each persona file
- Context of the request
- Stage of development (planning, design, implementation, etc.)

## Key Features

### Intelligent Routing
Each persona has `USE WHEN` triggers that help the CEO route requests intelligently. For example:
- "write code" → JAMStack Developer
- "review code" → Code Reviewer
- "write tests" → QA Engineer
- "is this secure?" → Security Analyst

### Create-Then-Review Pattern
The workflow encourages a collaborative approach:
1. Primary persona creates the work
2. Review personas improve and validate it
3. Multiple perspectives ensure quality

### Project Standards
The system enforces universal conventions:
- All documentation in `/docs` folder (except root README.md)
- Always read `README.md` for project-specific details
- Always read `.todo.md` for work history and context
- Always read `learnings.md` (if exists) for learned rules and preferences
- Conventional git commits
- Atomic commits only

### Self-Learning System
The system includes a RuleSmith persona that learns and documents workflows:
- Saves learned rules to `learnings.md` in the project root
- Always keeps `learnings.md` in context
- Appends new learnings (never overwrites) to maintain history
- Captures project-specific workflows, preferences, and standard practices

## File Structure

```
cursor_rules/
├── .cursorrules          # Configuration file (always/smart policies)
├── .todo.md              # Task planning and history
├── learnings.md          # Learned rules, workflows, and preferences
├── README.md             # This file
└── rules/
    ├── workflow.mdc              # Always: Pre-task planning process
    ├── project-standards.mdc     # Always: Universal conventions
    ├── ceo.mdc                   # Always: Persona orchestrator
    ├── learn.mdc                 # Smart: Self-learning system
    ├── product-manager.mdc       # Smart: Product management
    ├── software-architect.mdc    # Smart: System architecture
    ├── cfo.mdc                   # Smart: Financial analysis
    ├── vp-of-growth.mdc          # Smart: Growth strategy
    ├── coo.mdc                   # Smart: Operations
    ├── vc-partner.mdc            # Smart: Strategic perspective
    ├── jamstack-developer.mdc    # Smart: Code implementation
    ├── code-reviewer.mdc         # Smart: Code review
    ├── qa-engineer.mdc           # Smart: Quality assurance
    ├── security-analyst.mdc      # Smart: Security review
    ├── ui-ux-designer.mdc        # Smart: UI/UX design
    ├── technical-writer.mdc      # Smart: Documentation
    ├── devops-engineer.mdc       # Smart: DevOps tasks
    └── git-workflow.mdc          # Smart: Git operations
```

## Usage

### For New Projects
1. Copy this entire `cursor_rules` folder to your project
2. Update your project's `README.md` with:
   - Technology stack
   - Project structure
   - Development setup
   - Project-specific conventions
3. Create `learnings.md` (optional) to document learned workflows and preferences
4. The system will automatically read your README, `.todo.md`, and `learnings.md` for project context

### Workflow Example
```
User: "I want to add user authentication"

AI (CEO): "Okay, putting on my Product Manager hat for this...
          What is the core user problem this feature is trying to solve?"

User: "Users need to log in to access their data"

AI (Product Manager): "Let me create user stories and acceptance criteria..."
[Creates .todo.md with plan]

User: "proceed"

AI (Software Architect): "I'll design the authentication system..."
AI (JAMStack Developer): "I'll implement the FastAPI endpoints..."
AI (Code Reviewer): "Let me review the code quality..."
AI (QA Engineer): "I'll write tests for this..."
AI (Security Analyst): "Let me check for security vulnerabilities..."
AI (Technical Writer): "I'll document this in /docs..."
```

## Persona Details

Each persona file includes:
- **USE WHEN** triggers for intelligent activation
- Core abilities and focus areas
- Characteristic questions they ask
- Best practices they follow

## Customization

### Adding New Personas
1. Create a new `.mdc` file in `rules/`
2. Add `USE WHEN` triggers in the description frontmatter
3. Add the persona to `ceo.mdc` expert team list
4. Add to `.cursorrules` with `"policy": "smart"`

### Modifying Workflow
Edit `rules/workflow.mdc` to change the planning process or add new conventions.

### Project-Specific Standards
Each project should maintain:
- **`README.md`**: Technology stack, project structure, development setup, conventions
- **`.todo.md`**: Task planning and work history (auto-maintained by workflow)
- **`learnings.md`**: Learned workflows, preferences, and standard practices (auto-maintained by RuleSmith)

The system automatically reads these files to understand project context.

## Philosophy

This system is designed to:
- **Scale** across different projects and tech stacks
- **Enforce** best practices through personas
- **Plan** before executing to avoid mistakes
- **Collaborate** through multiple expert perspectives
- **Learn** and adapt through the RuleSmith persona and `learnings.md` file

## License

This cursor rules system is designed to be reusable across projects. Customize as needed for your team and workflow.
