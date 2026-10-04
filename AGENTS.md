# Agent instructions

## This repo

Atlas holds Furio manifests of open source systems, under `systems/<system>/<repo>/.architecture/`.
There is no manifest at the repo root. The rules in `README.md` ("How a system is mapped") are
binding: pin every manifest to an upstream release, take every component and relation from a
file of that release and name it in `SOURCES.md`, leave out what cannot be verified, write
descriptions and diagrams from scratch.

Check with `npx @getfurio/cli validate systems/*/*/`, and build the map with
`npx @getfurio/cli build --workspace atlas --site --out _site systems/*/*/` (the trailing slash
matters: without it `SOURCES.md` is taken for a repo).

This repo is public. Nothing personal or private enters it: commits use a GitHub noreply address,
and no home folder path, personal email address or name of a private project appears in files,
commits or pull requests. `scripts/guard.mjs` checks it in the git hooks
(`git config core.hooksPath scripts/hooks`) and on every pull request.

<!-- furio:start -->
## Architecture manifest (Furio)

This repo describes the architecture it owns in `.architecture/architecture.yaml`. When a change
adds, removes or renames a service, function, job, frontend, queue, topic, database, cache, storage
bucket or external API, or changes who calls, publishes, consumes, reads or writes what, update the
manifest in the same change and run `npx @getfurio/cli validate`. Full instructions:
`.agents/skills/furio-architecture/SKILL.md`.
<!-- furio:end -->
