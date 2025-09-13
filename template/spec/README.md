# Spec Overview

This folder contains the project’s core specifications used as the single source of truth for design and indirect code generation.

- models/: Data models defined with JSON Schema (YAML).
- api/: OpenAPI 3.1 definitions for REST endpoints.
- rules/: Business rules in Markdown.
- flows/: User/process flows in Markdown.

Guidelines
- Filenames use kebab-case; headings start with a single H1.
- Keep specs concise, consistent, and implementation-agnostic.
- Treat these files as inputs for analysis and indirect code scaffolding in Laravel.

