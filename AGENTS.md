# AGENTS.md

This repository is an Obsidian vault. Follow the vault maintenance rules in
`AI-Obsidian-Rules.md`.

Key rules:

1. Prefer Obsidian MCP for vault reads, writes, moves, deletes, and link verification.
2. Direct filesystem edits are a last-resort fallback, except agent rule files may be edited directly so tools can discover them reliably.
3. File and folder paths must not contain spaces; replace every space with `-`.
4. Keep Obsidian wikilinks valid, resolvable, and readable.
5. Before deleting duplicates, compare them first and keep the normalized no-space path.
6. Verify after maintenance work that there are no space paths, malformed links, missing targets, or stale duplicate references.
7. Every modification must be committed to git after verification, using an all-English standard git commit message.
