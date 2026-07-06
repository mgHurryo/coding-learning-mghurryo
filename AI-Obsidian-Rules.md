---
title: AI Obsidian Rules
description: Rules for AI agents working in this Obsidian vault
tags:
  - AI
  - Obsidian
  - Rules
---

# AI Obsidian Rules

This note defines the working rules for AI agents maintaining this Obsidian vault.

## Core Principle

All repository maintenance should preserve Obsidian usability: file paths must be clean, links must resolve, and visible link text must remain readable.

## Required Tooling

1. Use the Obsidian MCP server for all vault modifications whenever possible.
2. This includes reading files, writing files, modifying content, moving files, deleting files, checking directories, and verifying links.
3. Direct filesystem operations are allowed only as a last-resort fallback when the Obsidian MCP server cannot perform the required action.
4. Rules files for coding agents may be edited directly when needed so the target tool can discover them reliably.
5. If a fallback is used, explain why MCP was insufficient and verify the result afterward with Obsidian MCP when practical.

## Path Naming Rules

1. File names and folder names must not contain spaces.
2. Replace every space in a path with `-`.
3. This applies to English names, Chinese-English mixed names, and any other path segment.
4. Prefer stable, readable kebab-style names such as `RESTful-API-Design.md` or `JWT-无状态认证.md`.
5. After renaming, update all affected Obsidian links and verify that no old path references remain.

## Obsidian Link Rules

1. Obsidian wikilinks must use valid syntax.
2. Do not leave malformed links such as extra closing brackets, for example `]]]`.
3. Link targets must resolve to existing notes.
4. Avoid ambiguous link targets when a full vault-relative path would be clearer.
5. Link display text must be normal and readable.
6. Do not leave empty aliases such as `[[Target|]]`.
7. In Markdown tables, escaped pipes such as `\|` may be preserved when needed to keep the table structure valid.

## Deduplication Rules

1. Before deleting duplicates, read and compare candidate files through Obsidian MCP.
2. Prefer keeping the normalized path version, especially the version with no spaces and `-` separators.
3. Confirm that useful backlinks point to the retained file or can be safely updated.
4. Delete only after confirming the duplicate is obsolete.
5. After deletion, verify that old targets are no longer present in directories or link search results.

## Verification Checklist

After maintenance work, verify through Obsidian MCP that:

1. No file or folder path contains spaces.
2. No malformed wikilinks remain.
3. No internal links point to deleted or missing notes.
4. No ambiguous links remain where full paths are required.
5. Link display text is not empty or obviously abnormal.
6. Duplicate old-path files are removed.
7. Directory listings show only the normalized files.

## Operating Style

1. Make changes conservatively and in small batches.
2. Read before modifying.
3. Verify after modifying.
4. Report what changed and what was verified.
5. Do not create files with spaces in their names.
6. Every modification must be committed to git after verification, using an all-English standard git commit message.
