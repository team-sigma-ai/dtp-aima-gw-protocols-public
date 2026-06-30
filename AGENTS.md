# AGENTS.md

Guidance for anyone (human or agent) working with **`dtp-aima-gw-protocols-public`**,
the external-facing gRPC / protobuf API contract for the AIMA Gateway. Read this
before integrating against the contract or opening a PR.

## What this repo is

- Protobuf definitions for the AIMA Gateway's **external-facing** API. External
  consumers use these directly, or via generated SDKs, so every file here is a
  published contract.
- The repo has no build of its own; it is `.proto` files, compiled by downstream
  consumers.

## If you're implementing against these services

- **Consuming the contract.** The protos are vendored (for example as a git
  submodule) and compiled into client and server stubs by the consuming
  service's own gRPC toolchain.
- **Need gRPC or protobuf integration details?** Consult your operator, and refer
  to the official, canonical resources: the gRPC documentation
  (<https://grpc.io>) and the Protocol Buffers documentation
  (<https://protobuf.dev>). Do not guess at toolchain specifics from this repo.
- **Versioning is by package.** The version lives in the package path
  (`...v1.<area>`, `...v2.<area>`), not in message or service names. Types stay
  plain (`ChatRequest`, `Token`).
- **A published version is frozen.** It stays forwards-compatible: within a `vN`
  package only additive, backward-compatible changes are made (new fields with
  new numbers), so code generated against a version keeps working. A consumer
  can always upgrade to a newer release of the same version without changes.
- **A change of shape or semantics arrives as a new `vN` package** with its own
  services and messages. Adopting it is an explicit opt-in; your existing
  integration is untouched. A service may keep the same name across versions
  (`v1.…CorpusRetrieval`, `v2.…CorpusRetrieval`); the package keeps them distinct.
- **Field stability.** Field numbers and types are never changed or reused, and
  fields are never removed or renamed within a published version. A superseded
  field is marked with a `// Deprecated: …` doc comment but stays on the wire,
  its number and type unchanged.
- **Finding things.** The layout is `proto/vN/<area>/services.proto` (for
  example `proto/v1/generative/services.proto` or
  `proto/v1/management/keys/services.proto`). The package mirrors the path
  (`ai.sigmafinancial.aima.vN.<area>.services`); the directory is plural
  (`keys`), the package segment singular (`key`). Imports use the full
  repo-rooted path.

## If you're contributing

Changes here alter the published external contract; treat them accordingly.

- **Authorisation first.** A change to these protos changes what external
  consumers depend on. Get sign-off before starting, and never merge without
  approval.
- **Forwards compatibility is the priority.** Avoid breaking changes. Code
  generated against a published version must keep compiling and working against
  every later release of that version.
- **If in doubt, follow the existing patterns in the codebase.** Match the
  surrounding files for style, naming, field numbering, and layout rather than
  inventing a new convention.
- **Branching.** Branch `feature/<id>` (or `fix/` / `chore/`) off `main`, and
  keep `main` linear (rebase, never merge commits). Open a PR to `main`.
- **Keep it minimal; it's external-facing.**
  - *Comments:* the absolute minimum, purpose and intent only, in the existing
    house style (`// The token id`). No rationale, no "why", no design-doc
    references, no ticket numbers in the proto. Fuller reasoning lives in design
    docs or the internal protocols repo.
  - *PR descriptions:* the same rule. Give a plain changelog of what was added
    (new messages, services, RPCs); no rationale, no cross-references. A linked
    ticket may carry the internal detail, but is not required here.
- **Additive changes only within a version.** A new field takes the next unused
  number, following the surrounding file's numbering (commonly increments of 5).
  Adding a field changes the generated stubs, so update every call site that
  builds the message.
- **A new shape means a new `vN` package**, never a breaking edit to a frozen
  version.
- **Deprecation.** Mark a superseded field with a doc comment (`// Deprecated: …`)
  only. Do not use the proto `[deprecated = true]` option; some code generators
  emit a deprecation attribute that breaks downstream builds which deny
  warnings. Keep the field on the wire, its number and type unchanged.
- **Validate before opening a PR.** The contract must compile clean, with no
  warnings, in the consuming service(s).
- **Maintain this document.** When a new house-style rule emerges, update
  `AGENTS.md` in the same change so it stays the single source of truth.
- **Commits.** Use [Conventional Commits](https://www.conventionalcommits.org/),
  GPG-signed. Do not reference any machine- or environment-specific tools or
  configuration anywhere (commit message, PR title, or description). That
  includes coding agents and `Co-authored-by` sign-offs; they leak internal
  details and are a security concern.
