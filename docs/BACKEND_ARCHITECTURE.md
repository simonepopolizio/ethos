# Backend Architecture for Ethos Projects

> **Companion to**: [MONOREPO_OPTIONS.md](./MONOREPO_OPTIONS.md)
> **Last Updated**: 2025-11-12

## Overview

This document clarifies backend architecture for each Ethos project within the monorepo. The recommended monorepo structure supports **both frontend and backend** code.

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

**Recommended Stack**:
```
Frontend: React Native
Backend:  Python (FastAPI) for ML + Node.js for API
Database: PostgreSQL + MongoDB (flexible schema for products)
ML:       Python (TensorFlow/PyTorch) for OCR
Blockchain: Ethereum/Polygon (if using)
```

**Monorepo Structure**:
```
apps/
└── price-transparency/         # React Native app
    └── src/

services/
├── price-transparency-api/     # Node.js/Express API
│   ├── src/
│   │   ├── routes/
│   │   ├── services/
│   │   └── blockchain/         # Web3 integration
│   └── package.json
└── price-transparency-ml/      # Python ML service
    ├── src/
    │   ├── ocr/                # Image recognition
    │   └── analysis/           # Price analysis
    └── pyproject.toml
```

**Why Hybrid Backend**:
- ✅ Python for ML/AI workloads
- ✅ Node.js for API (same language as frontend)
- ✅ Services communicate via REST/gRPC

---

### 5. Knowledge Framework (Learning Platform)

**Backend Needs**:
- User progress tracking
- Content management
- AI-powered personalization
- Learning path generation
- Assessment system

**Recommended Stack**:
```
Frontend: React + Next.js
Backend:  Python (FastAPI) for ML + Next.js API routes
Database: PostgreSQL + Vector DB (Pinecone/Weaviate)
ML:       Python (TensorFlow/PyTorch)
Cache:    Redis
```

**Monorepo Structure**:
```
apps/
└── knowledge-framework/        # Next.js app
    ├── src/
    │   ├── app/                # Frontend
    │   └── api/                # Light API routes
    └── package.json

services/
├── knowledge-framework-ml/     # Python ML service
│   ├── src/
│   │   ├── personalization/    # Adaptive algorithms
│   │   ├── recommendations/    # Content recommendations
│   │   └── assessment/         # Learning assessment
│   └── pyproject.toml
└── knowledge-framework-api/    # Python FastAPI (main backend)
    ├── src/
    │   ├── routes/
    │   ├── services/
    │   └── models/
    └── pyproject.toml
```

**Why Python Backend**:
- ✅ Better for ML/AI workloads
- ✅ FastAPI is fast and modern
- ✅ Type hints + async support
- ✅ Easy ML model integration

---

### 6. IdeologyBase (NLP + Visualization)

**Backend Needs**:
- Video transcription pipeline
- NLP processing (transformers)
- RAG system (ChromaDB)
- Semantic search
- Data analysis API
- Position mapping

**Recommended Stack**:
```
Frontend: React + D3.js/Three.js for visualization
Backend:  Python (FastAPI) - ML-heavy workload
Database: PostgreSQL + ChromaDB (vector DB)
ML:       Python (transformers, LangChain)
Queue:    Celery + Redis (async processing)
```

**Monorepo Structure**:
```
apps/
└── ideologybase-web/           # React visualization app
    └── src/

services/
├── ideologybase-api/           # FastAPI main backend
│   ├── src/
│   │   ├── routes/
│   │   │   ├── transcriptions.ts
│   │   │   ├── search.ts
│   │   │   └── analysis.ts
│   │   └── services/
│   └── pyproject.toml
│
├── ideologybase-nlp/           # NLP processing pipeline
│   ├── src/
│   │   ├── transcribe.py       # Video → text
│   │   ├── process.py          # Text processing
│   │   └── classify.py         # Position classification
│   └── pyproject.toml
│
└── ideologybase-rag/           # RAG system
    ├── src/
    │   ├── ingest.py           # ChromaDB ingestion
    │   ├── query.py            # Semantic search
    │   └── embeddings.py       # Vector embeddings
    └── pyproject.toml
```

**Why All Python**:
- ✅ ML/NLP heavy workload
- ✅ Best ecosystem for transformers
- ✅ ChromaDB is Python-native
- ✅ LangChain integration

---

## Complete Monorepo Structure (Frontend + Backend)

```
ethos/
├── apps/                           # Frontend + Full-stack apps
│   ├── ethosmesh/                 # Next.js (frontend + API routes)
│   ├── ethostask/                 # Next.js + tRPC (full-stack)
│   ├── new-calendar-web/          # React (frontend only)
│   ├── new-calendar-mobile/       # React Native
│   ├── price-transparency/        # React Native
│   ├── knowledge-framework/       # Next.js (frontend + light API)
│   └── ideologybase-web/          # React (visualization)
│
├── services/                       # Backend microservices
│   ├── new-calendar-api/          # Node.js Express (shared API)
│   ├── price-transparency-api/    # Node.js Express
│   ├── price-transparency-ml/     # Python ML service
│   ├── knowledge-framework-api/   # Python FastAPI
│   ├── knowledge-framework-ml/    # Python ML service
│   ├── ideologybase-api/          # Python FastAPI (main API)
│   ├── ideologybase-nlp/          # Python NLP pipeline
│   └── ideologybase-rag/          # Python RAG system
│
├── packages/                       # Shared libraries (frontend + backend)
│   ├── shared-ui/                 # React components
│   ├── shared-utils/              # TS utilities (isomorphic)
│   ├── shared-types/              # TypeScript types
│   ├── design-system/             # Design tokens
│   ├── api-client/                # API client library
│   ├── calendar-core/             # Business logic
│   └── config/                    # Shared configs
│
├── infrastructure/
│   ├── docker/                    # Dockerfiles for services
│   ├── k8s/                       # Kubernetes manifests
│   └── terraform/                 # Infrastructure as Code
│
└── tools/
    └── scripts/
```

---

## Backend Technology Recommendations

### When to Use Each Backend

| Backend Tech | Use Case | Projects |
|--------------|----------|----------|
| **Next.js API Routes** | Simple CRUD, full-stack apps | EthosMesh, EthosTask |
| **tRPC** | Type-safe APIs, monorepo apps | EthosTask |
| **Node.js Express/Fastify** | Separate API for mobile, REST APIs | New Calendar, Price Transparency |
| **Python FastAPI** | ML/AI workloads, NLP, data science | Knowledge Framework, IdeologyBase |
| **GraphQL** | Complex data relationships, flexible queries | Optional for any project |

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

The recommended monorepo structure **fully supports**:

- ✅ **Next.js full-stack apps** (frontend + API routes)
- ✅ **Separate Node.js APIs** (Express/Fastify)
- ✅ **Python backend services** (FastAPI for ML/AI)
- ✅ **Shared code** between frontend and backend
- ✅ **Type safety** across the stack (TypeScript + Python type hints)
- ✅ **Unified development** experience (one repo, one command)
- ✅ **Independent deployment** (each service can deploy separately)

### Recommended Backend Architecture by Complexity

**Simple Projects** → Next.js API Routes (EthosMesh, EthosTask)
**Mobile + Web** → Separate Express/Fastify API (New Calendar)
**ML/AI Heavy** → Python FastAPI + Node.js (Knowledge Framework, IdeologyBase)
**Hybrid** → Mix of all above (Price Transparency)

---

## Next Steps

1. ✅ Review this backend architecture
2. ✅ Decide on backend technology per project
3. ✅ Follow [MONOREPO_OPTIONS.md](./MONOREPO_OPTIONS.md) for setup
4. ✅ Implement backends incrementally (Phase 3-6)

---

[← Back to Monorepo Options](./MONOREPO_OPTIONS.md) | [← Back to Main](../README.md)
