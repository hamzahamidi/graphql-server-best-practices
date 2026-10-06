# GraphQL Server Best Practices

[![Latest release](https://img.shields.io/github/v/release/hamzahamidi/graphql-server-best-practices?display_name=tag)](https://github.com/hamzahamidi/graphql-server-best-practices/releases/latest)
[![MIT License](https://img.shields.io/github/license/hamzahamidi/graphql-server-best-practices)](LICENSE)

A portable skill and practical reference for people and coding agents working on GraphQL servers. It focuses on field resolvers, DataLoader and equivalent batching, query cost, authorization, and schema design.

The guidance is framework aware. It asks an agent to inspect the codebase and the installed framework version before recommending a pattern. It does not require a resolver for every field: use the default resolver when the parent already contains the value. Use nested field resolvers for independently loaded relationships, and use DataLoader or an equivalent when repeated lookups can be batched through a real bulk operation. `Promise.all` is for independent concurrent work, not a substitute for batching.

## Installation

### Codex

For a project, copy `skills/graphql-server-best-practices` into that project's `.agents/skills/` directory. Codex will discover the `SKILL.md` there. For personal use, copy it to `~/.agents/skills/graphql-server-best-practices/`. See the [Codex skills guide](https://developers.openai.com/codex/skills/) for discovery scopes.

### Claude Code

The marketplace manifest is `.claude-plugin/marketplace.json`; the plugin manifest is `.claude-plugin/plugin.json`. Add this repository as a marketplace, then install the plugin:

```sh
claude plugin marketplace add hamzahamidi/graphql-server-best-practices
claude plugin install graphql-server-best-practices@graphql-server-best-practices
```

### Other supported coding agents

Install the skill with the open source [`skills` CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add hamzahamidi/graphql-server-best-practices --skill graphql-server-best-practices
```

The CLI can install it for supported agents such as Claude Code, Codex, Cursor, and Windsurf. See the [CLI documentation](https://www.skills.sh/docs/cli) for agent options and telemetry settings.

Browse the listing on [Skills.sh](https://www.skills.sh/hamzahamidi/graphql-server-best-practices/graphql-server-best-practices).

## Usage examples

Use the skill by asking an agent to apply it to a concrete GraphQL task. For example:

### Review a resolver for N+1 queries

```text
Trace the Post.author field from its resolver to the data source. Check whether
the current code makes one backend request per post, whether a real bulk lookup
exists, and how the repository scopes and authorizes its DataLoader. Recommend
the smallest change that follows the installed framework and local conventions.
```

### Implement a nested field

```text
Implement Post.author using this repository's existing resolver and loader
patterns. Keep independent field behavior in the field resolver, preserve
authorization, and follow the loader's result ordering and cache scope. Use
Promise.all only for independent work, not to issue one request per post.
```

### Optional OpenAI plugin package

The skill installs directly in Codex using the instructions above. This repository also includes a portable OpenAI plugin package. To submit that package to the OpenAI plugin directory, create a ZIP with `plugin.json` at its root, then upload it in the [OpenAI Platform Plugins dashboard](https://platform.openai.com/plugins). The directory requires a verified developer identity, automated checks, and OpenAI review before publication. See the [submission guide](https://developers.openai.com/plugins/deploy/submission). A GitHub release does not automatically add the plugin to that directory.

The `v1.0.3` package has been submitted to OpenAI and is under review. It is not publicly listed yet.

To create an archive for a tagged release, set `VERSION` to the tag's numeric version:

```sh
VERSION=1.0.2
git archive --format=zip --output="graphql-server-best-practices-${VERSION}.zip" "v${VERSION}"
```

## Guidance and sources

Read [`skills/graphql-server-best-practices/SKILL.md`](skills/graphql-server-best-practices/SKILL.md) for the full checklist, examples, and links to the GraphQL specification, GraphQL.org, DataLoader, and Apollo documentation. The file is the single source of the technical guidance.

See [`PRIVACY.md`](PRIVACY.md) for the plugin privacy policy.

## License

MIT. See [`LICENSE`](LICENSE).

## Contributing

Issues and pull requests are welcome. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for the review criteria and contribution steps. Use the issue forms in GitHub to report a problem or propose a change.
