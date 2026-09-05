# Coverage

Coverage is a focused screenwriting workspace for outlining, drafting, and revising a screenplay with reviewable AI assistance.

[Project site](https://coveragehq.github.io/Coverage/) · [Issues](https://github.com/coveragehq/Coverage/issues)

## Highlights

- **Screenplay-aware editor** — Industry-standard scene headings, action, character cues, dialogue, parentheticals, transitions, dual dialogue, and title pages.
- **Visual outlining** — Develop acts and beats on the Beat Board without leaving the writing workspace.
- **Three AI modes** — Ask questions about the draft, propose structured edits, or develop the outline.
- **Reviewable changes** — AI suggestions remain pending until the writer accepts or rejects them.
- **Full-project context** — The assistant can reason across screenplay elements, page count, scenes, characters, and beats.
- **Writing tools** — Scene navigation, character tracking, notes, snapshots, revision management, writing goals, statistics, and scene comparison.
- **Export and print** — Layout-aware pagination, print preview, and PDF export.
- **Durable projects** — PostgreSQL-backed project storage with local browser persistence for resilience.

## Architecture

Coverage is composed of four services:

- **Web client** — React 18, TypeScript, and Vite.
- **Project API** — Node.js, Express, and `pg` for projects, writing goals, and writing sessions.
- **AI service** — Python, FastAPI, OpenAI Agents, and server-sent events for streaming responses and tool activity.
- **Database** — PostgreSQL 16, storing complete screenplay documents as JSONB alongside writing metadata.

Nginx serves the production client and proxies `/api` to the project API and `/ai-api` to the AI service. Docker Compose runs the complete stack locally.

## Tech Stack

- React 18 and TypeScript
- Vite
- Node.js and Express
- Python and FastAPI
- OpenAI API and OpenAI Agents SDK
- PostgreSQL 16
- Docker Compose and Nginx
- jsPDF
- Vitest and ESLint
- Langfuse integration for optional AI observability

## Quick Start with Docker

### Requirements

- Docker Desktop or Docker Engine with Compose
- An OpenAI API key for AI features

Create a root environment file:

```bash
cp server-python/.env.example .env
```

Replace the placeholder `OPENAI_API_KEY`, then start the stack:

```bash
docker compose up --build
```

Open [http://localhost:5173](http://localhost:5173).

PostgreSQL data is retained in the `postgres_data` Docker volume. Stop the stack with:

```bash
docker compose down
```

## Local Development

### Requirements

- Node.js 20 or newer
- Python 3.11 or newer
- PostgreSQL 16, or Docker for running PostgreSQL

Start PostgreSQL:

```bash
docker compose up -d postgres
```

Install and run the project API:

```bash
cd server
npm install
npm run dev
```

In another terminal, install and run the AI service:

```bash
cd server-python
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
# Add your OPENAI_API_KEY to .env
python main.py
```

In a third terminal, run the web client:

```bash
npm install
npm run dev
```

The development server proxies the project API to port `3001` and the AI service to port `3002`.

## Configuration

Docker Compose accepts these environment variables:

- `OPENAI_API_KEY` — Required for AI features.
- `DB_USER`, `DB_PASSWORD`, `DB_NAME`, `DB_PORT` — PostgreSQL connection settings.
- `LANGFUSE_ENABLED` — Enables or disables Langfuse observability.
- `LANGFUSE_HOST`, `LANGFUSE_PUBLIC_KEY`, `LANGFUSE_SECRET_KEY` — Langfuse connection settings.
- `LANGFUSE_LOG_CONTENT` — Controls whether screenplay and response content is included in observability data. It defaults to `false`.
- `VITE_API_BASE_URL` — Optional project API target for the Vite development proxy.
- `VITE_AI_API_TARGET` — Optional AI service target for the Vite development proxy.

## Development Commands

Run these commands from the repository root:

```bash
npm run dev       # Start the Vite development server
npm run build     # Type-check and build the production client
npm run lint      # Run ESLint
npm test          # Run frontend unit tests once
npm run test:watch
```

Python tests use pytest:

```bash
cd server-python
pip install pytest
pytest
```

## Keyboard Shortcuts

- `Tab` — Cycle screenplay element types.
- `Enter` — Create the next screenplay element.
- `Backspace` — Delete an empty element.
- `↑` / `↓` — Move between elements.
- `Cmd/Ctrl + N` — Create a screenplay.
- `Cmd/Ctrl + /` — Toggle AI chat.
- `F1` — Open keyboard help.

The complete shortcut reference is available from the Help button inside the application.

## Data and Privacy

Projects are persisted to PostgreSQL through the project API. The browser also keeps local data to preserve work during temporary API or network failures.

AI requests may contain screenplay content so the selected model can reason about the complete project. Langfuse content logging is disabled by default; review your OpenAI and Langfuse configuration before using sensitive material.

## Contributing

Bug reports, feature proposals, and pull requests are welcome. Start with the [issue tracker](https://github.com/coveragehq/Coverage/issues).

Before opening a pull request, run:

```bash
npm run build
npm run lint
npm test
```

## License

No license has been added yet. Until a license is selected, standard copyright restrictions apply even though the source is publicly available.

