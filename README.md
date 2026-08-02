<h1 align="center">Big Sky</h1>

<h3 align="center">Fast, opinionated, ours.</h3>

<p align="center">
  A Nuxt frontend, a Rust API, and a Postgres database — wired together and ready on day one.
</p>

<p align="center">
  <img src="public/header.png" alt="Big Sky" width="720">
</p>

## The Stack

Three layers, each in its own top-level directory.

| Name | Layer | Built with |
| --- | --- | --- |
| **Big Sky** | The whole stack | — |
| **Horizon** | Frontend | Vue & Nuxt |
| **Bedrock** | Backend | Rust & Axum |
| **Cellar** | Database | PostgreSQL & SQLx |

```
Browser  ->  horizon  ->  bedrock  ->  cellar
             (Nuxt)      (Axum API)   (Postgres)
             :3000       :8080        :5432
```

`horizon` renders the UI and talks to `bedrock` over HTTP. `bedrock` owns all business logic and is the only thing that touches the database. `cellar` stores the data, and nothing else reaches it directly.

## Why we named it

The parts of a web app don't come with good names — they come with jargon. So we gave ours real ones. What we landed on is a cross-section of the land itself, top to bottom.

- **Big Sky** — the whole thing. Everything above, below, and in between.
- **Horizon** — the frontend. The visible edge. What you actually look at.
- **Bedrock** — the backend. Structural, load-bearing, out of sight.
- **Cellar** — the database. Dug in, cool and dark, and it remembers everything.

## Layout

```
big-sky/
├── horizon/     Nuxt frontend
├── bedrock/     Axum API server
├── cellar/      Postgres service definition
├── nix/         Reproducible dev environment
└── public/      Repo assets
```
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
