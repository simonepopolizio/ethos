# Ethos Monorepo Strategy: Options & Recommendations

> **Last Updated**: 2025-11-12
> **Status**: Proposal for Discussion

## Executive Summary

This document presents monorepo management options for the Ethos project ecosystem, which consists of 6 distinct projects spanning web, mobile, ML/AI, blockchain, and GIS technologies.

**Recommended Approach**: **Nx + pnpm workspaces** with Changesets for versioning

---

## Table of Contents

1. [Project Analysis](#project-analysis)
2. [Monorepo Options](#monorepo-options)
3. [Recommended Architecture](#recommended-architecture)
4. [Implementation Roadmap](#implementation-roadmap)
5. [Alternative Approaches](#alternative-approaches)
6. [Decision Criteria](#decision-criteria)

---

## Project Analysis

### Current Projects & Technical Requirements

| Project | Type | Tech Stack (Likely) | Key Requirements |
|---------|------|---------------------|------------------|
| **EthosMesh** | Web App | React/Node.js + GIS (Leaflet/PostGIS) | Decentralized architecture, map visualization, data dashboards |
| **EthosTask** | Web App | React/Next.js + PostgreSQL | API-first design, real-time updates, project management |
| **New Calendar** | Web + Mobile | React/Next.js + React Native | Hybrid digital/physical, calendar integration, community features |
| **Price Transparency** | Mobile App | React Native + ML/Blockchain | Mobile scanning, data visualization, blockchain tracking |
| **Knowledge Framework** | Web Platform | React + Python ML | AI integration, adaptive learning, personalization |
| **IdeologyBase** | Web + NLP Services | React + Python (transformers, ChromaDB, RAG) | NLP pipeline, vector DB, data visualization |

### Shared Needs Across Projects

- **Code Sharing**: UI components, utilities, types, business logic
- **Independent Development**: Different teams, different maturity levels
- **Coordinated Releases**: Some shared infrastructure
- **Open Source**: Public repository, community contributions
- **Polyglot Support**: JavaScript/TypeScript + Python + potentially Rust/Solidity
- **Efficient CI/CD**: Fast builds, smart testing, cost-effective

---

## Monorepo Options

### Option 1: Nx + pnpm (RECOMMENDED)

**Best For**: Multi-language projects with advanced CI/CD needs

#### Pros
- ✅ **Advanced caching**: Up to 7x faster builds than alternatives
- ✅ **Distributed execution**: Run tasks across multiple CI machines
- ✅ **Polyglot support**: Orchestrates JS/TS, Python, and other languages
- ✅ **Intelligent affected detection**: Only build/test what changed
- ✅ **Free remote caching**: Nx Cloud free tier (500GB + 500 hours/month)
- ✅ **Excellent DX**: AI coding assistant integration, generators, visualization
- ✅ **Large ecosystem**: Plugins for React, Next.js, React Native, Node.js, Python
- ✅ **pnpm efficiency**: 60-80% disk space savings, 3-5x faster installs

#### Cons
- ⚠️ Steeper learning curve initially
- ⚠️ More configuration overhead
- ⚠️ More opinionated about structure

#### Cost
- **$0** for open source projects (unlimited GitHub Actions + Nx Cloud free tier)

#### Example Structure
```
ethos/
├── apps/
│   ├── ethosmesh/              # React + GIS
│   ├── ethostask/              # Next.js app
│   ├── new-calendar-web/       # Next.js
│   ├── new-calendar-mobile/    # React Native
│   ├── price-transparency/     # React Native
│   └── knowledge-framework/    # React + ML
├── packages/
│   ├── shared-ui/              # React components
│   ├── shared-utils/           # TypeScript utilities
│   ├── shared-types/           # Type definitions
│   └── design-system/          # Design tokens
├── services/
│   ├── ideologybase-nlp/       # Python NLP
│   ├── ideologybase-rag/       # Python RAG
│   └── ml-models/              # Python ML
├── nx.json                     # Nx configuration
├── package.json                # Root dependencies
├── pnpm-workspace.yaml         # pnpm workspaces
└── pyproject.toml              # Python workspace
```

---

### Option 2: Turborepo + pnpm

**Best For**: JavaScript/TypeScript-only projects prioritizing simplicity

#### Pros
- ✅ **Minimal configuration**: Very quick setup
- ✅ **Excellent caching**: Fast incremental builds
- ✅ **Simple mental model**: Less opinionated
- ✅ **Vercel integration**: Native support for Vercel deployments
- ✅ **Good performance**: Written in Rust

#### Cons
- ⚠️ **JavaScript/TypeScript only**: No native polyglot support (problematic for Python services)
- ⚠️ **No distributed execution**: Can't split tasks across CI machines
- ⚠️ **Less comprehensive**: Fewer built-in tools than Nx
- ⚠️ **Manual affected detection**: Need to implement yourself

#### Cost
- **$0** for open source

#### Why Not Recommended for Ethos
- Cannot handle Python services (IdeologyBase, ML components) effectively
- Lacks distributed execution needed for large CI workloads
- Limited scalability as projects grow

---

### Option 3: Bazel

**Best For**: Enterprise-scale polyglot codebases (Google-scale)

#### Pros
- ✅ **True polyglot**: Native support for many languages
- ✅ **Extreme scalability**: Handles billions of lines of code
- ✅ **Hermetic builds**: Completely reproducible
- ✅ **Advanced distributed execution**: Enterprise-grade

#### Cons
- ❌ **Extremely complex**: Steep learning curve
- ❌ **Requires dedicated team**: Build engineering expertise needed
- ❌ **Verbose configuration**: BUILD files for every package
- ❌ **Limited JS ecosystem integration**: Not designed for Node.js projects
- ❌ **Overkill**: Best for 1000+ developer teams

#### Why Not Recommended for Ethos
- Far too complex for early-stage projects
- Would require dedicated build engineering team
- Better alternatives exist for this scale

---

### Option 4: Simple Workspaces (pnpm only)

**Best For**: Very small projects, minimal tooling

#### Pros
- ✅ **Ultra simple**: Just package manager features
- ✅ **No additional tools**: pnpm built-in workspaces
- ✅ **Fast setup**: Minimal configuration

#### Cons
- ⚠️ **No task orchestration**: Manual script management
- ⚠️ **No caching**: Rebuild everything every time
- ⚠️ **No affected detection**: Test everything on every change
- ⚠️ **Poor CI efficiency**: Expensive CI times
- ⚠️ **JavaScript/TypeScript only**: No Python support

#### Why Not Recommended for Ethos
- Too minimal for 6 projects with complex dependencies
- CI costs would be high without caching
- Doesn't solve polyglot needs

---

## Recommended Architecture

### Technology Stack

| Layer | Tool | Purpose |
|-------|------|---------|
| **Orchestration** | Nx | Task running, caching, affected detection |
| **JS/TS Packages** | pnpm workspaces | Dependency management, workspace linking |
| **Python Packages** | uv | Modern, fast Python package manager |
| **Versioning** | Changesets | Independent versioning, changelog generation |
| **CI/CD** | GitHub Actions | Workflow automation |
| **Remote Caching** | Nx Cloud | Shared cache across team/CI |
| **Linting** | ESLint + Ruff | JavaScript/TypeScript + Python |
| **Formatting** | Prettier + Black | Code formatting |
| **Testing (JS/TS)** | Vitest | Fast, modern testing |
| **Testing (Python)** | pytest | Python testing |

### Workspace Organization

```
ethos/                          # Monorepo root
├── apps/                       # End-user applications
│   ├── ethosmesh/             # Territorial planning (React + GIS)
│   ├── ethostask/             # Task management (Next.js)
│   ├── new-calendar-web/      # Calendar web app
│   ├── new-calendar-mobile/   # Calendar mobile (React Native)
│   ├── price-transparency/    # Price tracker (React Native)
│   └── knowledge-framework/   # Learning platform
│
├── packages/                   # Shared JavaScript/TypeScript libraries
│   ├── shared-ui/             # React components
│   ├── shared-utils/          # Utilities
│   ├── shared-types/          # TypeScript types
│   ├── design-system/         # Design tokens
│   └── config/                # Shared configs (ESLint, TS, etc.)
│
├── services/                   # Backend services (Python)
│   ├── ideologybase-nlp/      # NLP pipeline
│   ├── ideologybase-rag/      # RAG system
│   └── ml-models/             # ML models
│
├── infrastructure/             # Infrastructure as Code
│   ├── terraform/
│   ├── docker/
│   └── k8s/
│
├── tools/                      # Development tools
│   ├── scripts/
│   └── generators/
│
├── docs/                       # Documentation
│
├── .github/                    # GitHub Actions workflows
│   └── workflows/
│
├── nx.json                     # Nx configuration
├── package.json                # Root package.json
├── pnpm-workspace.yaml         # pnpm workspace config
├── pyproject.toml              # Python workspace config
└── README.md
```

### Versioning Strategy

**Independent Versioning** (Recommended)

Each project maintains its own version:

```
Apps (user-facing):
  - @ethos/ethosmesh: v0.3.0
  - @ethos/ethostask: v0.2.1
  - @ethos/new-calendar-web: v0.1.0
  - @ethos/new-calendar-mobile: v0.1.0
  - @ethos/price-transparency: v0.1.0
  - @ethos/knowledge-framework: v0.2.0

Services:
  - ideologybase-nlp: v0.4.0
  - ideologybase-rag: v0.3.0
  - ml-models: v0.1.0

Shared Libraries:
  - @ethos/shared-ui: v1.2.3
  - @ethos/shared-utils: v1.1.0
  - @ethos/shared-types: v1.3.0
```

**Why Independent?**
- Projects are at different maturity levels
- Different release schedules needed
- Clear communication about what changed where
- Flexibility for different development speeds

**Managed with Changesets**:
- Developers write changelog entries with code changes
- Automated version bumping and CHANGELOG generation
- Simple workflow: `changeset add` → commit → `changeset version` → publish

---

## Implementation Roadmap

### Phase 1: Foundation (Weeks 1-2)

**Goal**: Set up monorepo infrastructure

**Tasks**:
1. Initialize Nx workspace with pnpm
2. Configure workspace structure (apps, packages, services)
3. Set up shared configurations (ESLint, Prettier, TypeScript)
4. Configure Git hooks (Husky + lint-staged)
5. Initialize Changesets for versioning
6. Set up basic GitHub Actions CI

**Deliverables**:
- ✅ Nx workspace running
- ✅ pnpm workspaces configured
- ✅ Shared tooling in place
- ✅ Basic CI pipeline

**Effort**: ~5-10 hours

---

### Phase 2: Shared Libraries (Weeks 3-4)

**Goal**: Create foundation libraries used by all apps

**Tasks**:
1. Create `@ethos/shared-ui` - React components
2. Create `@ethos/shared-utils` - Common utilities
3. Create `@ethos/shared-types` - TypeScript types
4. Create `@ethos/design-system` - Design tokens
5. Set up Storybook for component documentation
6. Write tests for shared libraries

**Deliverables**:
- ✅ 3-4 shared libraries published
- ✅ Documentation and examples
- ✅ High test coverage (>80%)

**Effort**: ~10-15 hours

---

### Phase 3: First Application (Weeks 5-6)

**Goal**: Build first complete app to validate architecture

**Recommended**: Start with **EthosTask** (simplest full-stack app)

**Tasks**:
1. Generate EthosTask app with Nx
2. Import shared libraries
3. Build core features
4. Set up deployment pipeline
5. Document patterns and practices

**Deliverables**:
- ✅ EthosTask MVP running
- ✅ Using shared libraries
- ✅ Deployed to staging environment
- ✅ Development patterns documented

**Effort**: ~20-30 hours (depending on scope)

---

### Phase 4: Expand Web Apps (Weeks 7-12)

**Goal**: Add remaining web applications

**Projects**:
- EthosMesh (with GIS integration)
- New Calendar (web version)
- Knowledge Framework

**Tasks**:
1. Create each application using established patterns
2. Extract common code to shared libraries
3. Set up project-specific CI/CD
4. Configure deployments

**Deliverables**:
- ✅ 4 web apps in monorepo
- ✅ Shared code extracted and reused
- ✅ Independent deployment pipelines

**Effort**: ~40-60 hours total

---

### Phase 5: Mobile Apps (Weeks 13-16)

**Goal**: Add React Native mobile applications

**Projects**:
- New Calendar Mobile
- Price Transparency

**Tasks**:
1. Set up React Native in monorepo
2. Share code with web apps (business logic, types)
3. Create mobile-specific UI components
4. Set up mobile build pipelines

**Deliverables**:
- ✅ 2 mobile apps
- ✅ Code sharing with web apps
- ✅ Mobile CI/CD

**Effort**: ~30-40 hours

---

### Phase 6: Python Services (Weeks 17-20)

**Goal**: Integrate Python ML/NLP services

**Projects**:
- IdeologyBase NLP pipeline
- IdeologyBase RAG system
- ML models service

**Tasks**:
1. Set up Python workspace with uv
2. Configure Nx to orchestrate Python tasks
3. Create service directories and structure
4. Set up Python testing and linting
5. Create Docker images for services
6. Integrate with web apps via APIs

**Deliverables**:
- ✅ 3 Python services
- ✅ Nx orchestrating Python + JS/TS
- ✅ Containerized services
- ✅ API integration with frontends

**Effort**: ~30-40 hours

---

### Phase 7: Optimization (Weeks 21-24)

**Goal**: Optimize performance and workflows

**Tasks**:
1. Enable Nx Cloud distributed execution
2. Fine-tune cache configurations
3. Optimize CI workflows
4. Set up deployment automation
5. Monitor build performance
6. Write comprehensive documentation

**Deliverables**:
- ✅ 50%+ CI time reduction
- ✅ Distributed builds operational
- ✅ Complete documentation
- ✅ Team onboarding guide

**Effort**: ~15-20 hours

---

## Alternative Approaches

### Alternative 1: Turborepo for Web-Only Start

**Scenario**: If you want to start with just web apps and delay Python services

**Approach**:
- Use Turborepo + pnpm for initial web apps
- Migrate to Nx later when adding Python services
- Lower initial complexity

**Pros**:
- Simpler to start
- Still gets caching benefits
- Easy migration path to Nx

**Cons**:
- Need to migrate later
- Missing advanced features from start
- No distributed execution

---

### Alternative 2: Multiple Repositories (Polyrepo)

**Scenario**: Keep each major project in its own repository

**Structure**:
```
Repos:
- ethos-ethosmesh
- ethos-ethostask
- ethos-new-calendar
- ethos-price-transparency
- ethos-knowledge-framework
- ethos-ideologybase
- ethos-shared (shared libraries)
```

**Pros**:
- Clear boundaries between projects
- Independent access control
- Simple git history per project

**Cons**:
- ❌ **Code sharing is difficult**: npm publishing overhead
- ❌ **Coordinated changes are painful**: Multiple PRs across repos
- ❌ **No atomic commits**: Can't update multiple projects at once
- ❌ **Dependency version hell**: Keeping shared libs in sync
- ❌ **Higher CI costs**: Each repo builds independently
- ❌ **Harder to onboard**: Multiple repos to understand

**Not Recommended** for Ethos due to high interdependency

---

### Alternative 3: Hybrid Approach

**Scenario**: Some projects in monorepo, some separate

**Structure**:
```
Monorepo:
- Web apps (EthosMesh, EthosTask, New Calendar, Knowledge Framework)
- Shared libraries

Separate Repos:
- Mobile apps
- Python ML services
```

**Pros**:
- Flexibility for different teams
- Can use language-specific tools

**Cons**:
- Complexity of managing both approaches
- Still have code sharing challenges
- Coordinated releases difficult

**Consider if**: Teams are geographically distributed or have very different workflows

---

## Decision Criteria

### Choose Option 1 (Nx + pnpm) if:

- ✅ You want the best long-term scalability
- ✅ You value advanced caching and CI optimization
- ✅ You're willing to invest time upfront for better DX later
- ✅ You plan to have multiple languages (JS/TS + Python)
- ✅ You want distributed task execution
- ✅ You expect the team to grow
- ✅ You want to minimize CI costs with smart caching

**→ RECOMMENDED for Ethos**

---

### Choose Option 2 (Turborepo) if:

- You want to start very simply
- You're only building web apps initially (no Python services)
- You're using Vercel for hosting
- You don't need distributed builds
- You have a very small team (<5)

---

### Choose Polyrepo if:

- Projects are truly independent (not the case for Ethos)
- Different access control needed per project
- No code sharing between projects
- Very different technology stacks with no overlap

**→ NOT RECOMMENDED for Ethos**

---

## Cost Analysis

### Nx + pnpm + GitHub Actions (Recommended)

| Service | Free Tier | Cost |
|---------|-----------|------|
| GitHub Actions | Unlimited for public repos | **$0** |
| Nx Cloud | 500GB cache + 500 hours/month | **$0** |
| Vercel | Unlimited for hobby projects | **$0** |
| pnpm | Open source | **$0** |
| Changesets | Open source | **$0** |

**Total: $0/month** for early-stage open source project

**When to upgrade**:
- Nx Cloud: When team >10 or need >500 hrs/month (~$50/month for team plan)
- Vercel: When traffic exceeds hobby limits (rare for early projects)
- GitHub Actions: Never for public repos

---

## Quick Start Commands

### If You Choose Nx + pnpm (Recommended)

```bash
# 1. Initialize Nx workspace
npx create-nx-workspace@latest ethos \
  --preset=npm \
  --package-manager=pnpm

cd ethos

# 2. Configure pnpm workspaces
cat > pnpm-workspace.yaml << EOF
packages:
  - 'apps/*'
  - 'packages/*'
  - 'services/*'
EOF

# 3. Create directory structure
mkdir -p apps packages services infrastructure tools

# 4. Install Changesets
pnpm add -DW @changesets/cli
pnpm changeset init

# 5. Set up Git hooks
pnpm add -DW husky lint-staged
npx husky init

# 6. Create first shared library
npx nx g @nx/react:library shared-ui \
  --directory=packages/shared-ui \
  --publishable \
  --importPath=@ethos/shared-ui

# 7. Create first application
npx nx g @nx/react:application ethostask \
  --directory=apps/ethostask \
  --style=css \
  --bundler=vite

# 8. Run the app
npx nx serve ethostask
```

---

## Next Steps

### Recommended Actions

1. **Review this document** with the team
2. **Discuss concerns** and preferences
3. **Make decision** on approach (Nx recommended)
4. **Allocate time** for Phase 1 setup (~5-10 hours)
5. **Follow implementation roadmap** step by step
6. **Iterate and improve** based on team feedback

### Questions to Answer

- [ ] Do we agree with polyglot monorepo approach?
- [ ] Are we comfortable with Nx learning curve?
- [ ] Should we start with all projects or incrementally migrate?
- [ ] Who will be responsible for monorepo maintenance?
- [ ] What's our timeline for Phase 1 completion?

---

## Resources

### Documentation
- **Nx**: https://nx.dev/
- **pnpm**: https://pnpm.io/
- **Changesets**: https://github.com/changesets/changesets
- **Monorepo Tools Comparison**: https://monorepo.tools/

### Example Open Source Monorepos
- **Nx**: https://github.com/nrwl/nx
- **Cal.com**: https://github.com/calcom/cal.com (Next.js monorepo)
- **shadcn/ui**: https://github.com/shadcn/ui (Component library)

### Getting Help
- **Nx Discord**: https://discord.gg/nx
- **GitHub Discussions**: Create discussions in this repo

---

## Appendix: Tool Comparison Matrix

| Feature | Nx + pnpm | Turborepo | Bazel | Simple pnpm |
|---------|-----------|-----------|-------|-------------|
| **JS/TS Support** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Python Support** | ⭐⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐⭐ | ⭐ |
| **Caching** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ |
| **Distributed Execution** | ⭐⭐⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐⭐ | ⭐ |
| **Ease of Setup** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐⭐ |
| **Learning Curve** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐⭐ |
| **Community** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Cost** | Free | Free | Free | Free |
| **CI Optimization** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ |
| **Affected Detection** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ |
| **Developer Experience** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |

**Legend**: ⭐⭐⭐⭐⭐ Excellent | ⭐⭐⭐⭐ Good | ⭐⭐⭐ Adequate | ⭐⭐ Limited | ⭐ Minimal/None

---

## Conclusion

For the Ethos project ecosystem, **Nx + pnpm workspaces** provides the optimal balance of:

- **Developer Experience**: Modern tooling with excellent DX
- **Performance**: Advanced caching and distributed execution
- **Flexibility**: Polyglot support for JS/TS and Python
- **Cost**: $0 for open source projects
- **Scalability**: Grows with the project from 1 to 100+ developers
- **Community**: Large ecosystem and active support

The initial learning investment pays off quickly through:
- 50-90% faster CI pipelines
- Efficient code sharing across projects
- Better developer productivity
- Lower long-term maintenance costs

**Recommendation**: Proceed with Nx + pnpm, following the phased implementation roadmap.

---

*This document is a living resource. Please provide feedback and suggest improvements as we learn from implementation.*

[← Back to Main](../README.md)
