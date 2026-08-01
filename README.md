<p align="center">
  <img src="public/header.jpg" alt="Big Sky" width="720">
</p>

# Big Sky

**Big Sky** is our starter template for web projects: a Nuxt frontend, a Rust API, and a Postgres database, wired together with sane defaults so a new project starts on day one instead of week one.

Clone it, rename it, build on it.

---

## The Stack

Three layers, each in its own top-level directory.

| Layer    | Name      | Built with                            |
| -------- | --------- | ------------------------------------- |
| Frontend | `horizon` | Vue 3, Nuxt 4, Nuxt UI, Tailwind CSS  |
| Backend  | `bedrock` | Rust, Axum, Tokio                     |
| Database | `cellar`  | PostgreSQL, via Docker Compose        |

```
Browser  ->  horizon  ->  bedrock  ->  cellar
             (Nuxt)      (Axum API)   (Postgres)
             :3000       :8080        :5432
```

`horizon` renders the UI and talks to `bedrock` over HTTP. `bedrock` owns all business logic and is the only thing that touches the database. `cellar` stores the data and nothing else reaches it directly.

## Layout

```
big-sky/
├── horizon/     Nuxt frontend
├── bedrock/     Axum API server
├── cellar/      Postgres service definition
├── nix/         Reproducible dev environment
└── public/      Repo assets
```

## Requirements

| Tool    | Version | Used by   |
| ------- | ------- | --------- |
| Node.js | 20+     | `horizon` |
| pnpm    | 11+     | `horizon` |
| Rust    | 1.85+   | `bedrock` |
| Docker  | recent  | `cellar`  |

Rust 1.85 is the floor for the 2024 edition. The exact pnpm version is pinned in `horizon/package.json`.

## Getting Started

```bash
git clone https://github.com/joncorv/napstack.git big-sky
cd big-sky
```

Then start each layer in its own terminal.

**Frontend — `horizon`**

```bash
cd horizon
pnpm install
pnpm dev          # http://localhost:3000
```

**Backend — `bedrock`**

```bash
cd bedrock
cargo run
```

**Database — `cellar`**

```bash
cd cellar
docker compose up -d
```

Only `horizon` is fully running today. See [Status](#status) for where the other two stand.

## Common Commands

| Command           | Directory | What it does                        |
| ----------------- | --------- | ----------------------------------- |
| `pnpm dev`        | `horizon` | Dev server with hot reload          |
| `pnpm build`      | `horizon` | Production build                    |
| `pnpm lint`       | `horizon` | ESLint                              |
| `pnpm typecheck`  | `horizon` | Type check with `vue-tsc`           |
| `cargo run`       | `bedrock` | Run the API server                  |
| `cargo test`      | `bedrock` | Run the test suite                  |
| `cargo clippy`    | `bedrock` | Lint                                |
| `cargo fmt`       | `bedrock` | Format                              |

## Configuration

Each layer will read its own `.env`, copied from a checked-in `.env.example`. The intended variables:

| Variable        | Layer     | Purpose                        |
| --------------- | --------- | ------------------------------ |
| `DATABASE_URL`  | `bedrock` | Postgres connection string     |
| `PORT`          | `bedrock` | Port the API listens on        |
| `NUXT_API_BASE` | `horizon` | Base URL for the `bedrock` API |

Nothing reads these yet. They are the convention to build against.

## Status

This is an early scaffold. Here is what is actually wired up today.

| Piece                          | Status      |
| ------------------------------ | ----------- |
| Nuxt app with Nuxt UI          | Working     |
| Axum project skeleton          | Scaffolded  |
| HTTP routes and handlers       | Planned     |
| Postgres Compose service       | Planned     |
| Database migrations            | Planned     |
| Nix dev shell                  | Planned     |
| CI pipeline                    | Planned     |
| Deployment                     | Planned     |

## About the Name

Big Sky is the open country the whole thing sits under. `bedrock` is the solid layer underneath that everything else is built on. `horizon` is the visible edge, the part people actually see. `cellar` is where things get stored and kept.

## License

MIT. See [`horizon/LICENSE`](horizon/LICENSE); a root license file is still to be added.
