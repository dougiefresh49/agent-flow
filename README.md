# Agent Flow

Agent Flow is a modular, extensible agent platform inspired by modern no-code and agent orchestration tools, built with Composio and LanggraphJS. It enables developers to rapidly build, customize, and extend AI-powered workflows using a visual node-based interface and robust API. The platform is designed for flexibility, developer experience, and seamless integration with third-party tools.

[Watch the Agent Flow demo video](agent_flow_web.mp4)

## Key Features

- **Visual Workflow Builder:** Drag-and-drop interface using ReactFlow for creating agent graphs (nodes and edges)
- **Core Node Types:**
  - **Input Node:** Entry point for data
  - **Output Node:** Where results are produced
  - **LLM Node:** Decision-making using large language models
  - **Tool Node:** Executes external actions
  - **Agent Node:** Combines LLM and Tool nodes for complex orchestration
- **API-First:** Each workflow is represented as a JSON graph and can be executed via a single API route
- **Composio Integration:** One SDK for hundreds of pre-built tools with automatic authentication handling
- **Agent Patterns:**
  - Prompt chaining
  - Parallelization
  - Routing
  - Evaluator-optimizer loops
  - Augmented LLMs with tool calls

## Tech Stack

- **Framework:** Next.js (React, TypeScript)
- **UI:** Tailwind CSS, shadcn/ui, ReactFlow
- **Agent Orchestration:** LangGraph
- **Tool Integration:** Composio SDK

## Example Workflow JSON

```json
{
  "nodes": [
    {
      "id": "input_1",
      "type": "customInput",
      "position": { "x": 100, "y": 100 },
      "data": { "label": "User Query" }
    },
    {
      "id": "agent_1",
      "type": "agent",
      "position": { "x": 300, "y": 100 },
      "data": { "tools": ["search", "summarize"] }
    },
    {
      "id": "output_1",
      "type": "customOutput",
      "position": { "x": 500, "y": 100 },
      "data": { "label": "Result" }
    }
  ],
  "edges": [
    { "source": "input_1", "target": "agent_1" },
    { "source": "agent_1", "target": "output_1" }
  ]
}
```

## Getting Started

1. **Install dependencies:**

   ```bash
   npm install
   ```

2. **Run the development server:**

   ```bash
   npm run dev
   ```

3. **Open your browser:**
   Visit [http://localhost:3000](http://localhost:3000) to see the app.

## Prerequisites

### Supabase Setup

1. **Create a Supabase project** at [https://supabase.com/](https://supabase.com/).

2. **Get your API URL and anon key:**

   - [API Settings (/settings/api)](https://supabase.com/dashboard/project/<projectID>/settings/api)
   - [Anon Key (/settings/api-keys)](https://supabase.com/dashboard/project/<projectID>/settings/api-keys)
   - Replace `<projectID>` with your actual Supabase project ID.

3. **Create the required `flows` table and enable RLS:**

   Run the following SQL in the [Supabase SQL Editor](https://supabase.com/dashboard/project/<projectID>/sql):

   ```sql
   create table public.flows (
     id uuid primary key default gen_random_uuid(),
     user_id uuid not null,
     name text not null,
     description text,
     graph_json jsonb,
     updated_at timestamp with time zone default now()
   );

   create index flows_user_id_idx on public.flows(user_id);

   -- Optional: Reference Supabase users table
   alter table public.flows add constraint flows_user_id_fkey foreign key (user_id) references auth.users(id);

   -- Enable RLS
   alter table public.flows enable row level security;

   -- Allow users to manage their own flows
   create policy "Users can manage their own flows" on public.flows
     for all
     using (auth.uid() = user_id);
   ```

### Composio Setup

1. **Sign up for Composio** at [https://composio.dev/](https://composio.dev/).
2. **Get your Composio API key** from your [Composio dashboard](https://app.composio.dev/).
3. **Configure your environment variables** as needed for Composio integration.

---

## Project Structure

- `app/` – Main application code (pages, api routes)
- `components/` - Components code
- `public/` – Static assets
- `README.md` – Project documentation

## Extending the Platform

- **Add new nodes:** Create new node types in the `components/` directory and register them in the workflow builder.
- **Authentication:** Composio handles OAuth, API keys, and other auth flows automatically via a unified modal.

## Contributing

- Fork the repo and create a feature branch
- Make your changes and submit a pull request
- Follow the code style and use modern React/Next.js best practices

## References

- Inspiration and architecture: [I Replicated Gumloop in a Weekend with Vibe Coding](https://lex.page/read/17128d4b-b703-4f6e-b5c5-16aa30b38d09)
- [Next.js Documentation](https://nextjs.org/docs)
- [shadcn/ui](https://ui.shadcn.com/)
- [Tailwind CSS](https://tailwindcss.com/)
