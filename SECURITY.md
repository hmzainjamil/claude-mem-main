# Security and data handling

## Scope

This repository contains a nested copy of the upstream Claude-Mem project under `claude-mem-main/`. The nested source implements an assistant memory system. Review upstream documentation and source for current behavior; the mirror's synchronization status is not established here.

## Sensitive data

Memory systems may persist prompts, observations, project context, transcripts, database records, or logs. Before installing or running the nested project:

- Review its current privacy controls, exclusions, storage paths, retention, and external provider connections.
- Treat stored memory and backups as sensitive.
- Use only data and model providers approved for your context.
- Keep secrets, customer records, and private prompts out of issues, commits, and public logs.
- Verify the source snapshot and changes against upstream.

## Reporting

Do not publish secrets or exploit details in a public issue. Use GitHub private vulnerability reporting when enabled, or contact the upstream maintainer through a private channel listed in the upstream project's GitHub profile. Report issues specific to this mirror to its repository owner privately. Include affected path/version, impact, and safe reproduction steps.

This guidance is not a security audit and does not certify the upstream project or this mirror.
