src/
├── app/                           # File-System Routing Layer (Sacred & Thin)
│   ├── layout.tsx                 # Root HTML/Body Wrapping & Global Providers
│   ├── page.tsx                   # Public Landing Page View
│   ├── (auth)/                    # Route Group: Isolated Authentication Flow
│   │   └── login/page.tsx
│   └── dashboard/                 # Core Protected Workspace Shell
│       ├── layout.tsx             # Instantiates the 3-Panel Resizable Flex Grid
│       └── page.tsx               # Workspace Portal Initialization
│
├── components/                    # Global Reusable Pure UI Primitives (Atom Level)
│   └── ui/                        # Non-Domain Aware Components (Shadcn Base)
│       ├── button.tsx
│       ├── progress.tsx
│       ├── dialogue.tsx
│       └── resizable.tsx
│
├── features/                      # Domain Modules (Molecule / Organism / Page Level)
│   ├── syndicate/                 # Collaborative Club Logic Domain
│   │   ├── components/            # Domain-Specific Atomic Files
│   │   │   ├── voting-card.tsx    # Handles Proposal Render & Websocket Local States
│   │   │   ├── sidebar-item.tsx   # Individual Active Room Row UI
│   │   │   └── room-scribe.tsx    # Persistent Right-Side Streaming Feed Card
│   │   ├── hooks/                 # Custom Real-Time State Subscriptions
│   │   │   └── useRoomSocket.ts   
│   │   └── services/              # Direct API Transaction Handlers
│   │       └── propose.api.ts     
│   │
│   ├── research/                  # AI Standalone Sandbox Logic Domain
│   │   ├── components/
│   │   │   ├── analysis-chat.tsx
│   │   │   └── canvas-chart.tsx
│   │   └── hooks/
│   │       └── useLiteLLMStream.ts
│   │
│   └── billing/                   # Subscription & Gate Domain
│       └── components/
│           └── compliance-modal.tsx
│
├── lib/                           # Centralized Infrastructure Core Assemblies
│   ├── db.ts                      # PostgreSQL Prisma/Drizzle Connection Pool
│   └── litellm.ts                 # Backend AWS Bedrock API Secure Transport Init
│
└── types/                         # Global Absolute Type Safety Specifications
    └── index.ts                   # Holds Strict User, Room, and Proposal Schemas