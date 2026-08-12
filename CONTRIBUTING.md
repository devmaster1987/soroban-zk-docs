# Contributing to Soroban ZK Docs

Thank you for helping improve the Soroban ZK Toolkit documentation! This guide explains how to set up the site locally, write new content, and submit changes.

## Prerequisites

- Node.js ≥ 18
- pnpm (recommended) or npm

## Setup

```bash
git clone git@github.com:soroban-zk-toolkit/soroban-zk-docs.git
cd soroban-zk-docs
pnpm install
pnpm start
```

The site will be available at `http://localhost:3000`.

## Writing documentation

All documentation lives in the `docs/` directory as Markdown files. Docusaurus supports standard Markdown plus MDX (JSX in Markdown).

### Frontmatter

Each page should include a frontmatter block:

```md
---
sidebar_position: 2
---

# Page Title
```

`sidebar_position` controls the order within its category. Lower numbers appear first.

### Directory structure

```
docs/
├── intro.md                    # Landing page
├── concepts/                   # ZK theory explainers
├── getting-started/            # Installation, quickstart, tutorials
├── contracts/                  # Contract reference
├── circuits/                   # Circuit reference
├── sdk/                        # SDK API reference
├── tutorials/                  # Step-by-step tutorials
└── faq.md                      # Frequently asked questions
```

### Adding a new page

1. Create a `.md` file in the appropriate subdirectory.
2. Add frontmatter with `sidebar_position`.
3. Update `sidebars.ts` if you are adding a new top-level category.

### Code examples

Use fenced code blocks with language identifiers:

````md
```ts
const x = 1;
```
````

Supported languages: `ts`, `tsx`, `js`, `bash`, `rust`, `circom`, `json`, `toml`.

## Submitting changes

1. Fork the repository.
2. Create a branch: `git checkout -b docs/my-improvement`.
3. Make your changes and commit with a descriptive message.
4. Open a pull request against `main`.

### Commit message format

Use the `docs(scope): description` convention:

```
docs(sdk): add MerkleTree.verify example
docs(concepts): fix Groth16 formula typo
docs(faq): add replay attack Q&A
```

## Style guide

- Use **sentence case** for headings (not Title Case).
- Prefer short sentences and active voice.
- Always include a working code example when documenting an API.
- Link to related pages rather than repeating content.
- Use tables for parameter references.

## Review process

All PRs require one review from a maintainer. CI runs a broken-link check and Markdown linter on every PR.

## Questions?

Open an issue with the `documentation` label, or ask in the `#zk-toolkit` channel on Stellar Discord.
