# GraphQL Server Best Practices

A portable skill and practical reference for people and coding agents working on GraphQL servers. It focuses on field resolvers, DataLoader and equivalent batching, query cost, authorization, and schema design.

The guidance is framework aware. It asks an agent to inspect the codebase and the installed framework version before recommending a pattern. It does not require a resolver for every field: use the default resolver when the parent already contains the value. Use nested field resolvers for independently loaded relationships, and use DataLoader or an equivalent when repeated lookups can be batched through a real bulk operation. `Promise.all` is for independent concurrent work, not a substitute for batching.

## Use the skill

### Codex

For a project, copy `skills/graphql-server-best-practices` into that project's `.agents/skills/` directory. Codex will discover the `SKILL.md` there. For personal use, copy it to `~/.agents/skills/graphql-server-best-practices/`.

### Claude Code

The marketplace manifest is `.claude-plugin/marketplace.json`; the plugin manifest is `.claude-plugin/plugin.json`. Add this repository as a marketplace, then install the plugin:

```sh
claude plugin marketplace add hamzahamidi/graphql-server-best-practices
claude plugin install graphql-server-best-practices@graphql-server-best-practices
```

### ChatGPT and Codex plugin package

This repository includes a portable OpenAI plugin package and the skill. To submit it to the ChatGPT and Codex plugin directory, create a ZIP with `plugin.json` at its root, then upload it in the [OpenAI Platform Plugins dashboard](https://platform.openai.com/plugins). The directory requires a verified developer identity, automated checks, and OpenAI review before publication. See the [submission guide](https://developers.openai.com/plugins/deploy/submission). A GitHub release does not automatically add the plugin to that directory.

After the `v1.0.0` tag exists, create the package with:

```sh
git archive --format=zip --output=graphql-server-best-practices-1.0.0.zip v1.0.0
```

## Guidance and sources

Read [`skills/graphql-server-best-practices/SKILL.md`](skills/graphql-server-best-practices/SKILL.md) for the full checklist, examples, and links to the GraphQL specification, GraphQL.org, DataLoader, and Apollo documentation. The file is the single source of the technical guidance.

## License

MIT. See [`LICENSE`](LICENSE).

## Contributing

Issues and pull requests are welcome. Keep recommendations tied to primary documentation or clearly label them as tradeoffs. Framework specific advice should name the relevant implementation and version.
