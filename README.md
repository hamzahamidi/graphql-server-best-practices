# GraphQL Server Best Practices

A portable skill and practical reference for people and coding agents working on GraphQL servers. It focuses on field resolvers, DataLoader and equivalent batching, query cost, authorization, and schema design.

The guidance is framework aware. It asks an agent to inspect the codebase and the installed framework version before recommending a pattern. It does not require a resolver for every field: use the default resolver when the parent already contains the value. Use nested field resolvers for independently loaded relationships, and use DataLoader or an equivalent when repeated lookups can be batched through a real bulk operation. `Promise.all` is for independent concurrent work, not a substitute for batching.

## Use the skill

### Codex

For a project, copy `skills/graphql-server-best-practices` into that project's `.agents/skills/` directory. Codex will discover the `SKILL.md` there. You can also install the skill in your personal skills directory.

### Claude Code

Add this repository as a marketplace, then install the plugin:

```sh
claude plugin marketplace add hamzahamidi/graphql-server-best-practices
claude plugin install graphql-server-best-practices@graphql-server-best-practices
```

### ChatGPT and Codex plugin package

This repository includes a portable OpenAI plugin manifest and the skill. To install it outside Codex project skills, package the repository as a ZIP and upload it through the Plugins settings or plugin submission portal. A public GitHub release does not automatically add it to the OpenAI directory.

## Guidance and sources

Read [`skills/graphql-server-best-practices/SKILL.md`](skills/graphql-server-best-practices/SKILL.md) for the full checklist, examples, and links to the GraphQL specification, GraphQL.org, DataLoader, and Apollo documentation. The file is the single source of the technical guidance.

## License

MIT. See [`LICENSE`](LICENSE).

## Contributing

Issues and pull requests are welcome. Keep recommendations tied to primary documentation or clearly label them as tradeoffs. Framework specific advice should name the relevant implementation and version.
