# Contributing

Thanks for helping improve this GraphQL server skill. Keep changes focused on guidance that can help people working across GraphQL server frameworks.

## Propose a change

Open an issue for a problem or a larger proposal. Use a pull request for a focused correction or addition. Describe the situation the change addresses and the behavior an agent should follow.

## Write framework-aware guidance

Tie behavior claims to primary documentation, such as the GraphQL specification, GraphQL.org, or the documentation for the relevant server or loader library. Name the framework and version when advice depends on an implementation detail. Label trade-offs as trade-offs, and avoid presenting one framework's convention as universal.

Keep the skill consistent with its core guidance: nested fields own their independent data access, DataLoader or an equivalent batches repeated lookups through a real bulk operation, and `Promise.all` handles independent concurrent work rather than replacing batching.

## Before opening a pull request

- Keep examples small and adaptable to other frameworks.
- Update `skills/graphql-server-best-practices/SKILL.md` when changing the source guidance.
- Update `docs/sources.md` when adding or replacing a source.
- Check that README instructions and manifest paths match the repository.
- Explain any framework-specific assumptions in the pull request.

The repository is licensed under MIT.
