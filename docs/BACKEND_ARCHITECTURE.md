# Backend Architecture for Ethos Projects

> **Companion to**: [MONOREPO_OPTIONS.md](./MONOREPO_OPTIONS.md)
> **Last Updated**: 2025-11-12

## Overview

This document clarifies backend architecture for each Ethos project within the monorepo. The recommended monorepo structure supports **both frontend and backend** code.

### Architecture Philosophy

**JavaScript/TypeScript First**: Use Node.js for all backends by default to maximize code sharing, type safety, and developer consistency. Only introduce Python when Node.js cannot reasonably handle the workload (heavy ML/NLP).

**Benefits**:
- ✅ End-to-end TypeScript type safety
- ✅ Maximum code sharing across frontend/backend
- ✅ Single language for most of the codebase
- ✅ Simpler monorepo management
- ✅ Easier team onboarding

---

## Backend Strategy by Project

### 1. EthosMesh (Territorial Planning)

**Backend Needs**:
- Map data management
- User authentication
- Resource inventory storage
- Voting/decision tracking
- GIS data processing

**Recommended Stack**:
```
Frontend: React + Leaflet/Mapbox
Backend:  Node.js (Next.js API routes) OR separate Express API
Database: PostgreSQL + PostGIS extension (geospatial)
Real-time: WebSocket for collaborative features
```

**Monorepo Structure**:
```
apps/
├── ethosmesh/              # Next.js full-stack app
│   ├── src/
│   │   ├── app/            # Frontend pages
│   │   ├── api/            # API routes (backend)
│   │   └── lib/            # Shared utilities
│   └── package.json

OR (if separate backend):

apps/
├── ethosmesh-web/          # React frontend
└── ethosmesh-api/          # Express/Fastify backend
```

**Why Next.js API Routes**:
- ✅ Full-stack in single app
- ✅ Simplified deployment
- ✅ Good for early stage
- ✅ Can migrate to separate API later

---

### 2. EthosTask (Task Management)

**Backend Needs**:
- Task CRUD operations
- Organization management
- Milestone tracking
- Real-time collaboration
- Notification system
- API for third-party integrations

**Recommended Stack**:
```
Frontend: React + Next.js
Backend:  Next.js API routes + tRPC (type-safe API)
Database: PostgreSQL
Real-time: WebSocket or Pusher
Cache:    Redis (optional)
```

**Monorepo Structure**:
```
apps/
└── ethostask/
    ├── src/
    │   ├── app/                    # Frontend
    │   ├── server/                 # tRPC backend
    │   │   ├── routers/            # API routes
    │   │   │   ├── tasks.ts
    │   │   │   ├── milestones.ts
    │   │   │   └── organizations.ts
    │   │   └── trpc.ts             # tRPC setup
    │   └── lib/
    │       └── db/                 # Database client
    └── package.json

packages/
└── api-types/                      # Shared API types
    └── src/
        └── ethostask.ts
```

**Why tRPC**:
- ✅ End-to-end type safety
- ✅ No code generation needed
- ✅ Great DX with autocomplete
- ✅ Shares types between frontend/backend

---

### 3. New Calendar (Web + Mobile)

**Backend Needs**:
- Calendar event storage
- Community event management
- User preferences
- Notification scheduling
- Cross-platform sync

**Recommended Stack**:
```
Frontend: React (web) + React Native (mobile)
Backend:  Node.js Express/Fastify (separate API)
Database: PostgreSQL
Cache:    Redis for sync
API:      REST or GraphQL
```

**Monorepo Structure**:
```
apps/
├── new-calendar-web/           # React web app
├── new-calendar-mobile/        # React Native app
└── new-calendar-api/           # Express/Fastify API
    ├── src/
    │   ├── routes/
    │   │   ├── events.ts
    │   │   ├── users.ts
    │   │   └── sync.ts
    │   ├── services/
    │   └── db/
    └── package.json

packages/
├── calendar-core/              # Shared business logic
│   └── src/
│       ├── date-utils.ts
│       └── event-utils.ts
└── calendar-types/             # Shared types
    └── src/
        └── index.ts
```

**Why Separate API**:
- ✅ Shared by web and mobile
- ✅ Can scale independently
- ✅ Clear separation of concerns

---

### 4. Price Transparency (Mobile)

**Backend Needs**:
- Product scanning/OCR
- Price history storage
- Supply chain data aggregation
- Community contributions
- Blockchain integration (optional)

**Recommended Stack (JavaScript-First)**:
```
Frontend:   React Native
Backend:    Node.js (Express/Fastify)
Database:   PostgreSQL + MongoDB (flexible schema)
OCR:        Cloud API (Google Vision, AWS Textract) OR TensorFlow.js
Blockchain: Ethereum/Polygon (ethers.js, web3.js)
```

**Monorepo Structure (JavaScript-Only)**:
```
apps/
└── price-transparency/         # React Native app
    └── src/

services/
└── price-transparency-api/     # Node.js API
    ├── src/
    │   ├── routes/
    │   │   ├── products.ts
    │   │   ├── scanning.ts
    │   │   └── analysis.ts
    │   ├── services/
    │   │   ├── ocr.ts          # Cloud OCR integration
    │   │   └── blockchain.ts   # Web3 integration
    │   └── ml/                 # TensorFlow.js (if self-hosted)
    └── package.json
```

**Why Node.js**:
- ✅ Same language as frontend (code sharing)
- ✅ Blockchain: ethers.js/web3.js are Node-native
- ✅ OCR: Use cloud APIs (Google Vision, AWS Textract)
- ✅ Alternative: TensorFlow.js for self-hosted OCR
- ✅ Unified codebase with TypeScript types

**Python Alternative** (Only if heavy self-hosted ML needed):
- Add separate `price-transparency-ml` Python service
- Use only for custom ML models not available in JS

---

### 5. Knowledge Framework (Learning Platform)

**Backend Needs**:
- User progress tracking
- Content management
- AI-powered personalization
- Learning path generation
- Assessment system

**Recommended Stack (JavaScript-First)**:
```
Frontend: React + Next.js
Backend:  Next.js (full-stack with API routes)
Database: PostgreSQL + Pinecone/Supabase Vector (managed)
AI:       OpenAI API / Anthropic API (cloud)
Cache:    Redis
```

**Monorepo Structure (JavaScript-Only)**:
```
apps/
└── knowledge-framework/        # Next.js full-stack app
    ├── src/
    │   ├── app/                # Frontend pages
    │   ├── api/                # API routes
    │   │   ├── users/
    │   │   ├── content/
    │   │   ├── progress/
    │   │   └── ai/             # AI integration endpoints
    │   ├── lib/
    │   │   ├── db/             # Database client
    │   │   ├── ai/             # OpenAI/Anthropic client
    │   │   └── vector/         # Pinecone client
    │   └── services/
    │       ├── personalization.ts
    │       └── recommendations.ts
    └── package.json
```

**Why Node.js (Next.js)**:
- ✅ Full-stack TypeScript (end-to-end type safety)
- ✅ AI via cloud APIs (OpenAI, Anthropic, Google AI)
- ✅ Vector DB: Pinecone/Supabase have JS SDKs
- ✅ Personalization: Algorithms in TypeScript
- ✅ Simplified deployment (Vercel)
- ✅ Share code with frontend

**Python Alternative** (Only if custom ML models needed):
- Add `knowledge-framework-ml` service for self-hosted models
- Use only if cloud APIs insufficient or cost-prohibitive

---

### 6. IdeologyBase (NLP + Visualization)

**Backend Needs**:
- Video transcription pipeline
- NLP processing (text analysis)
- RAG system (semantic search)
- Semantic search
- Data analysis API
- Position mapping

**Recommended Stack (JavaScript-First, Python When Necessary)**:

**Phase 1 - JavaScript-Only (MVP)**:
```
Frontend: React + D3.js/Three.js for visualization
Backend:  Node.js (Express/Next.js)
Database: PostgreSQL + Pinecone (managed vector DB)
AI:       OpenAI API (transcription via Whisper API)
NLP:      OpenAI/Anthropic for text analysis
Search:   Pinecone semantic search
```

**Monorepo Structure (JavaScript-Only)**:
```
apps/
└── ideologybase/               # Next.js full-stack app
    ├── src/
    │   ├── app/                # Frontend (visualization)
    │   ├── api/                # API routes
    │   │   ├── transcribe/     # Whisper API integration
    │   │   ├── analyze/        # OpenAI text analysis
    │   │   └── search/         # Pinecone semantic search
    │   ├── lib/
    │   │   ├── openai.ts       # OpenAI client
    │   │   ├── pinecone.ts     # Vector DB client
    │   │   └── analysis.ts     # Analysis logic
    │   └── services/
    │       ├── transcription.ts
    │       └── rag.ts
    └── package.json
```

**Why Start with JavaScript**:
- ✅ Whisper API for transcription (no Python needed)
- ✅ OpenAI/Anthropic for NLP analysis
- ✅ Pinecone for vector search (JS SDK available)
- ✅ Unified TypeScript codebase
- ✅ Faster initial development
- ✅ Lower complexity

**Phase 2 - Add Python (If Needed)**:

**Only add if**:
- Need self-hosted models (cost savings at scale)
- Custom fine-tuned models
- Specialized NLP beyond cloud APIs
- Advanced transformers use cases

```
services/
├── ideologybase-nlp/           # Python NLP service
│   ├── src/
│   │   ├── transcribe.py       # Whisper (self-hosted)
│   │   ├── embeddings.py       # Custom embeddings
│   │   └── classify.py         # Custom classifiers
│   └── pyproject.toml
│
└── ideologybase-rag/           # Python RAG (ChromaDB)
    ├── src/
    │   ├── ingest.py
    │   └── query.py
    └── pyproject.toml
```

**Migration Path**:
1. Start with cloud APIs (JavaScript-only)
2. Monitor costs and performance
3. Add Python services only when:
   - API costs become significant (>$500/month)
   - Need custom models
   - Performance requires optimization

---

## Complete Monorepo Structure (Frontend + Backend)

### JavaScript-First Approach

```
ethos/
├── apps/                           # Frontend + Full-stack apps (JavaScript/TypeScript)
│   ├── ethosmesh/                 # Next.js full-stack (frontend + API routes)
│   ├── ethostask/                 # Next.js + tRPC full-stack
│   ├── new-calendar-web/          # React frontend
│   ├── new-calendar-mobile/       # React Native
│   ├── price-transparency/        # React Native
│   ├── knowledge-framework/       # Next.js full-stack (frontend + API routes)
│   └── ideologybase/              # Next.js full-stack (frontend + API routes)
│
├── services/                       # Backend services (Node.js by default)
│   ├── new-calendar-api/          # Node.js Express (shared web + mobile API)
│   └── price-transparency-api/    # Node.js Express (API + OCR integration)
│
├── services-python/                # Python services (only when necessary)
│   └── [Added only if needed for custom ML models]
│
├── packages/                       # Shared libraries (isomorphic JavaScript/TypeScript)
│   ├── shared-ui/                 # React components
│   ├── shared-utils/              # TS utilities (work in browser + Node.js)
│   ├── shared-types/              # TypeScript types (frontend + backend)
│   ├── design-system/             # Design tokens
│   ├── api-client/                # API client library
│   ├── calendar-core/             # Business logic
│   └── config/                    # Shared configs (ESLint, TS, etc.)
│
├── infrastructure/
│   ├── docker/                    # Dockerfiles (if needed for Python)
│   └── terraform/                 # Infrastructure as Code
│
└── tools/
    └── scripts/                   # Build/deploy scripts
```

### Key Benefits of This Structure

- ✅ **95% JavaScript/TypeScript**: Unified language across stack
- ✅ **End-to-end type safety**: Shared types from DB to UI
- ✅ **Maximum code reuse**: Same utilities, validators, types everywhere
- ✅ **Simpler monorepo**: No polyglot complexity initially
- ✅ **Easy to extend**: Add Python later if truly needed

---

## Backend Technology Recommendations (JavaScript-First)

### Default: Use JavaScript/TypeScript

| Backend Tech | When to Use | Projects |
|--------------|-------------|----------|
| **Next.js API Routes** | Full-stack apps, co-located with frontend | EthosMesh, EthosTask, Knowledge Framework, IdeologyBase |
| **tRPC** | Type-safe APIs within monorepo | EthosTask (recommended) |
| **Node.js Express/Fastify** | Separate API for mobile apps | New Calendar, Price Transparency |
| **Cloud AI APIs** | ML/AI via OpenAI/Anthropic/Google | Knowledge Framework, IdeologyBase |

### Only Add Python When

| Scenario | Solution |
|----------|----------|
| **Self-hosted ML models needed** | Add Python service with TensorFlow/PyTorch |
| **Custom fine-tuned models** | Add Python NLP service |
| **Cloud API costs >$500/month** | Migrate to self-hosted Python models |
| **Specialized transformers** | Add Python service with HuggingFace |

### Technology Decision Tree

```
Does the project need ML/AI?
├─ NO → Node.js (Next.js/Express)
│   └─ Examples: EthosMesh, EthosTask, New Calendar
│
└─ YES → Can cloud APIs handle it? (OpenAI, Anthropic, etc.)
    ├─ YES → Node.js + Cloud APIs
    │   └─ Examples: Knowledge Framework, IdeologyBase (initially)
    │
    └─ NO (need custom models) → Add Python service
        └─ Only when absolutely necessary
```

---

## Shared Backend Packages

### packages/api-client/

**Purpose**: Type-safe API client shared by all frontends

```typescript
// packages/api-client/src/ethostask.ts
export class EthosTaskClient {
  constructor(private baseUrl: string) {}

  async getTasks() {
    // Shared API client logic
  }
}

// Usage in apps/ethostask-web
import { EthosTaskClient } from '@ethos/api-client';
const client = new EthosTaskClient('/api');
```

---

### packages/shared-utils/

**Purpose**: Isomorphic utilities (work in frontend and backend)

```typescript
// packages/shared-utils/src/validation.ts
export function validateEmail(email: string): boolean {
  // Works in browser and Node.js
}

// Used in:
// - Frontend forms
// - Backend API validation
```

---

### packages/shared-types/

**Purpose**: TypeScript types shared between frontend and backend

```typescript
// packages/shared-types/src/ethostask.ts
export interface Task {
  id: string;
  title: string;
  status: 'pending' | 'in-progress' | 'completed';
}

export interface CreateTaskDTO {
  title: string;
  description?: string;
}

// Used in:
// - Frontend components
// - Backend API routes
// - API client
```

---

## Database Strategy

### Recommended Databases by Project

| Project | Primary DB | Additional | Reason |
|---------|-----------|------------|--------|
| **EthosMesh** | PostgreSQL + PostGIS | Redis (cache) | Geospatial queries |
| **EthosTask** | PostgreSQL | Redis (cache) | Relational data |
| **New Calendar** | PostgreSQL | Redis (sync) | Structured events |
| **Price Transparency** | PostgreSQL + MongoDB | - | Mixed: structured + flexible |
| **Knowledge Framework** | PostgreSQL + Vector DB | Redis | User data + embeddings |
| **IdeologyBase** | PostgreSQL + ChromaDB | Redis | Structured + vector search |

### Monorepo Database Management

```
infrastructure/
├── docker/
│   └── docker-compose.yml      # Local dev databases
│       ├── postgres:15
│       ├── redis:7
│       ├── mongodb:7
│       └── chromadb:latest
│
└── migrations/
    ├── ethosmesh/              # Project-specific migrations
    ├── ethostask/
    └── shared/                 # Shared tables (users, auth)
```

---

## API Communication Patterns

### Pattern 1: Next.js API Routes (Monolithic)

```typescript
// apps/ethostask/src/app/api/tasks/route.ts
export async function GET() {
  const tasks = await db.task.findMany();
  return Response.json(tasks);
}
```

**Pros**: Simple, co-located with frontend
**Cons**: Harder to reuse for mobile apps

---

### Pattern 2: tRPC (Type-safe)

```typescript
// apps/ethostask/src/server/routers/tasks.ts
export const tasksRouter = router({
  list: publicProcedure.query(async () => {
    return await db.task.findMany();
  }),
  create: publicProcedure
    .input(z.object({ title: z.string() }))
    .mutation(async ({ input }) => {
      return await db.task.create({ data: input });
    }),
});

// Frontend usage (fully typed!)
const { data: tasks } = trpc.tasks.list.useQuery();
```

**Pros**: End-to-end type safety, great DX
**Cons**: TypeScript only, learning curve

---

### Pattern 3: REST API (Separate Service)

```typescript
// services/new-calendar-api/src/routes/events.ts
app.get('/api/events', async (req, res) => {
  const events = await db.event.findMany();
  res.json(events);
});

// Consumed by both web and mobile
// apps/new-calendar-web + apps/new-calendar-mobile
```

**Pros**: Platform agnostic, reusable
**Cons**: No automatic type safety

---

### Pattern 4: Python FastAPI

```python
# services/ideologybase-api/src/routes/search.py
@app.post("/api/search")
async def search(query: SearchQuery) -> SearchResults:
    results = await rag_service.search(query.text)
    return SearchResults(results=results)

# Type safety with Pydantic
class SearchQuery(BaseModel):
    text: str
    limit: int = 10
```

**Pros**: Great for ML/AI, fast, modern
**Cons**: Separate from TypeScript (use OpenAPI for types)

---

## Nx Configuration for Backend

### Next.js API Routes

```json
// apps/ethostask/project.json
{
  "targets": {
    "serve": {
      "executor": "@nx/next:server",
      "options": {
        "dev": true
      }
    },
    "build": {
      "executor": "@nx/next:build"
    }
  }
}
```

---

### Node.js Express API

```json
// services/new-calendar-api/project.json
{
  "targets": {
    "serve": {
      "executor": "@nx/js:node",
      "options": {
        "buildTarget": "new-calendar-api:build",
        "watch": true
      }
    },
    "build": {
      "executor": "@nx/esbuild:esbuild",
      "options": {
        "main": "services/new-calendar-api/src/main.ts",
        "outputPath": "dist/services/new-calendar-api"
      }
    }
  }
}
```

---

### Python FastAPI Service

```json
// services/ideologybase-api/project.json
{
  "targets": {
    "serve": {
      "executor": "nx:run-commands",
      "options": {
        "command": "uv run uvicorn src.main:app --reload",
        "cwd": "services/ideologybase-api"
      }
    },
    "test": {
      "executor": "nx:run-commands",
      "options": {
        "command": "uv run pytest",
        "cwd": "services/ideologybase-api"
      }
    }
  }
}
```

---

## Development Experience

### Running Everything Locally

```bash
# Start all services in development mode
nx run-many --target=serve --all --parallel=10

# Or specific services
nx serve ethostask               # Next.js app (frontend + API)
nx serve new-calendar-api        # Express API
nx serve ideologybase-api        # Python FastAPI
nx serve ideologybase-web        # React frontend
```

### Docker Compose for Dependencies

```yaml
# infrastructure/docker/docker-compose.yml
services:
  postgres:
    image: postgres:15
    ports:
      - "5432:5432"

  redis:
    image: redis:7
    ports:
      - "6379:6379"

  chromadb:
    image: chromadb/chroma:latest
    ports:
      - "8000:8000"
```

```bash
# Start all databases
docker-compose -f infrastructure/docker/docker-compose.yml up -d
```

---

## Deployment Strategy

### Option 1: Vercel (Recommended for Next.js apps)

**Deploy**:
- EthosMesh (Next.js full-stack)
- EthosTask (Next.js + tRPC)
- Knowledge Framework (Next.js frontend)
- IdeologyBase (React frontend)

**Why**: Zero-config, automatic previews, edge functions

---

### Option 2: Railway/Render (Node.js APIs)

**Deploy**:
- new-calendar-api
- price-transparency-api

**Why**: Easy Node.js deployment, free tier

---

### Option 3: Docker + Cloud Run/Fly.io (Python services)

**Deploy**:
- All Python services (FastAPI, ML pipelines)

**Why**: Best for containerized Python workloads

---

### Option 4: Kubernetes (Production scale)

**Deploy**: Everything in k8s cluster

**When**: After proving product-market fit

---

## Summary

### Does the Monorepo Handle Backend? ✅ YES

The recommended monorepo structure **fully supports frontend and backend** with a **JavaScript-first approach**:

- ✅ **Next.js full-stack apps** (frontend + API routes in one app)
- ✅ **Separate Node.js APIs** (Express/Fastify for mobile)
- ✅ **End-to-end TypeScript** (shared types from DB to UI)
- ✅ **Cloud AI APIs** (OpenAI, Anthropic for ML/AI needs)
- ✅ **Python only when necessary** (custom ML models)
- ✅ **Maximum code sharing** (utilities, validators, types)
- ✅ **Unified development** (one language, one repo, one command)
- ✅ **Independent deployment** (each service deploys separately)

### Recommended Backend Architecture (JavaScript-First)

**All Projects Start With**: JavaScript/TypeScript (Next.js or Express)

| Project | Backend Solution | Why |
|---------|-----------------|-----|
| **EthosMesh** | Next.js API Routes | Full-stack, GIS in Node.js |
| **EthosTask** | Next.js + tRPC | Type-safe full-stack |
| **New Calendar** | Express API | Shared by web + mobile |
| **Price Transparency** | Express + Cloud OCR | Blockchain (web3.js) + AI APIs |
| **Knowledge Framework** | Next.js + OpenAI API | Cloud AI, no Python needed |
| **IdeologyBase** | Next.js + Whisper/Pinecone | Cloud AI initially, Python later if needed |

### When to Add Python

**Only add Python services when**:
1. Cloud API costs exceed $500/month
2. Custom ML models required (not available via API)
3. Specialized NLP beyond cloud offerings
4. Performance optimization needs self-hosted models

**Start JavaScript-first, add Python incrementally only when proven necessary.**

---

## Next Steps

1. ✅ Review this backend architecture
2. ✅ Decide on backend technology per project
3. ✅ Follow [MONOREPO_OPTIONS.md](./MONOREPO_OPTIONS.md) for setup
4. ✅ Implement backends incrementally (Phase 3-6)

---

[← Back to Monorepo Options](./MONOREPO_OPTIONS.md) | [← Back to Main](../README.md)
