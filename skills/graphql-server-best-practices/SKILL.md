---
name: graphql-server-best-practices
description: Design, implement, review, or debug GraphQL server schemas, resolvers, data loading, and performance. Use for GraphQL backend work in any language or server framework.
---

# GraphQL server best practices

Use this skill for GraphQL server work. It covers schema design, field resolvers, data loading, authorization, and query cost. Follow the conventions and architecture of the repository being changed. These instructions are defaults, not a reason to replace a working local pattern without evidence.

## Start with the repository

1. Identify the GraphQL implementation, framework and versions, schema style, resolver layout, context construction, service or repository boundaries, and existing loader conventions.
2. Trace the requested schema field to its resolver, data access, and callers. Read neighboring resolvers and relevant tests before choosing a pattern.
3. Preserve the repository's architecture. Keep transport concerns in resolvers and delegate business rules and data access through the established application layers.
4. If implementation details depend on a library version, check the version in the repository and use that version's official documentation.

## Model work around fields

GraphQL executes a selection set by resolving its fields. Keep each field's data and policy behavior with the type and field that own it.

1. Have a root resolver fetch the root object or collection it owns. Let nested fields resolve through their own field resolvers when they have independent data access, arguments, computation, or authorization.
2. A resolver returns the value of its own field. The GraphQL executor uses that value as the source for resolving child fields. For example, `Post.author` returns the author value, which becomes the source for fields such as `User.name`. Do not manually walk the selection tree and recursively resolve child fields in a parent resolver unless the repository has an intentional query planner or projection layer that owns this work.
3. Use the framework's default field resolver when the parent already contains the correctly shaped field value and no separate policy or lookup is needed. An explicit resolver for every scalar field is unnecessary.
4. Treat sibling fields as independent. Do not rely on one sibling resolver running before another or on hidden mutations to shared parent objects.
5. Eager loading, joins, bulk hydration, and compiled projections are valid optimizations when they fit the framework and repository, preserve authorization, and avoid fetching unnecessary data. Keep independent field behavior testable and do not manually resolve child selections in the root resolver as an ad hoc shortcut.

JavaScript example in resolver-map style. Adapt the syntax to the server framework in use:

```ts
const resolvers = {
  Query: {
    post: (_source, { id }, context) => context.posts.getById(id),
  },
  Post: {
    author: (post, _args, context) =>
      context.loaders.userById.load(post.authorId),
  },
};
```

Here `Query.post` returns a post value. `Post.author` returns the author value. The executor invokes child fields on that author, and the loader can batch author lookups across posts.

## Design a stable schema

1. Design types and fields around the concepts and operations clients need. Keep database tables, internal service boundaries, and storage details behind the API unless they are meaningful parts of the contract.
2. Choose nullability to match real guarantees and failure behavior. Check how a non-null child failure propagates to the nearest nullable ancestor, including list item and list nullability, before changing a field's nullability.
3. Use explicit input and result types for meaningful operations. Keep naming consistent with the existing schema and avoid broad generic JSON fields unless the domain needs an opaque value.
4. Evolve public fields compatibly. Prefer deprecation and a migration path before removing or changing a field clients may use.

## Batch relationship lookups

Prevent N+1 backend work when a nested field is resolved for many parent values.

1. Prefer the repository's DataLoader or equivalent batching abstraction for repeated key based lookups from nested fields. For example, resolve `Post.author` with a `userById` loader instead of issuing one user request for every post.
2. Make the batch function perform a real bulk lookup where the backend supports one. A loader wrapper that loops over keys and issues one backend request for each key does not remove N+1 work.
3. Follow the chosen loader's contract for result shape, key ordering, missing keys, errors, duplicate keys, scheduling, and cache behavior. JavaScript DataLoader expects one result per key in input order. Other libraries may use a different contract.
4. Include every result-affecting field argument in the cache key or use a separate loader instance. This includes pagination, filters, sort order, locale, and visibility options. A loader bound to request identity may use that request's authorization context without repeating it in every key. JavaScript DataLoader compares object keys by identity by default. Use a canonical scalar key or a stable `cacheKeyFn` for composite keys so equivalent keys can share a cache entry.
5. Scope loaders to a request or operation whenever results can vary by user, tenant, locale, permissions, or other request context. Never share a permission-sensitive loader cache globally across users. For long-lived subscriptions, check whether loaders and authorization context are refreshed or invalidated between events.
6. Pass the minimum authorization context required by the data access layer. A loader must not make an otherwise forbidden object visible just because it was fetched for another field.
7. After a successful mutation changes cached data, invalidate affected entity keys, alternate lookup keys, and relationship keys as needed. In JavaScript DataLoader, `prime()` does not replace an existing cached value, so clear the key before priming it with the new value. Follow the selected library's equivalent invalidation pattern.
8. Do not add a loader automatically when a field is already satisfied by its parent value, a single bounded query, or an existing bulk fetch. First identify the repeated backend call the loader would batch.

## Choose concurrency and batching deliberately

In JavaScript, `Promise.all` and DataLoader solve different problems. Use the runtime's equivalent concurrency primitive in other languages.

1. Use DataLoader to coalesce repeated lookups for many keys and to deduplicate same-key loads within its cache scope. This is the usual fix for per-parent nested lookups that create N+1 requests.
2. Use `Promise.all` when a single resolver has a small, known set of independent operations and parallel execution reduces latency. Bound or limit concurrency when the number of operations can grow with input size.
3. `Promise.all(keys.map(key => loader.load(key)))` can combine concurrent field work with batching. Queue sibling loads before awaiting them when the loader's scheduling model requires it. Verify how the selected loader dispatches batches.
4. Do not put a per-parent backend call in `Promise.all` as a substitute for batching. It still performs one backend call per parent, can fan out without a limit, and may make the N+1 problem faster without removing it.
5. Choose aggregate error handling to match the schema's nullability and error contract. In JavaScript, a rejected promise makes `Promise.all` reject, while the other operations continue; use per-item handling or an all-settled form when partial outcomes are intended.
6. Do not create promises for every child inside the root resolver merely to pre-resolve the requested graph. Return the root field's value and let child field resolvers use request loaders or another deliberate bulk strategy.
7. In JavaScript, return a promise directly when no local error mapping, cleanup, or instrumentation requires awaiting its settlement.

## Protect data and resources

1. Authenticate the request and make identity available through the established request context. Enforce authorization at the field or domain operation that protects the data. Check nested fields too when they have different visibility rules.
2. Keep authorization in the data access path where the repository's architecture requires it. Do not assume that hiding a field in a client, or checking only a root resolver, protects every access path.
3. Paginate potentially large list fields, set a maximum page size, and validate pagination arguments.
4. Apply query cost or complexity budgets, rate limits, timeouts, and input-size limits when appropriate for the API's exposure and traffic. Consider depth, aliases and field breadth, and transport-level request batching where the server supports them. Make timeouts reach downstream work where possible. Do not invent fixed limits without checking operational requirements. Persisted-query caching alone is not an allowlist.
5. Keep resolver errors consistent with the API's contract. Represent expected business outcomes in schema types when clients need to branch on them. Return sanitized errors for unexpected failures and keep diagnostic detail in server logs.
6. For HTTP GraphQL, follow the server framework's guidance for CSRF protection, rejecting mutations over GET, and response cache isolation for authenticated data.
7. Keep query fields and nested fields side-effect-free and idempotent. Put writes in top-level mutation field resolvers. The GraphQL specification serializes top-level mutation fields, but this does not make the operation transactional. Do not rely on ordering among query fields or nested fields.

## Review the result

Before finishing, check the changed path against these questions:

1. Does each nested relationship resolve at its owning field, through a default resolver, or through a deliberate eager or bulk strategy that fits the repository?
2. Do repeated key lookups use a real bulk operation through a request-scoped loader or equivalent? Does its result follow the selected library's contract?
3. Is `Promise.all` limited to independent work rather than being used to hide per-parent N+1 calls?
4. Are authorization, nullability, not-found behavior, and errors consistent with the schema and neighboring fields?
5. Are potentially large collections and user-controlled inputs bounded? Could aliases, field breadth, transport batching, or nested relationships trigger expensive work?
6. Did the change follow the repository's service and data access boundaries?
7. Did verification exercise nested fields, batching behavior, missing keys, and request isolation where those behaviors changed?

## References

Use these primary sources for framework-neutral behavior and implementation details:

* [GraphQL execution and field resolvers](https://graphql.org/learn/execution/)
* [GraphQL specification](https://spec.graphql.org/September2025/)
* [DataLoader documentation](https://github.com/graphql/dataloader)
* [Apollo Server data fetching](https://www.apollographql.com/docs/apollo-server/data/fetching-data)
* [Apollo GraphQL security overview](https://www.apollographql.com/docs/graphos/platform/security/overview)
* [Apollo demand-oriented schema design](https://www.apollographql.com/docs/graphos/schema-design/guides/demand-oriented-schema-design)
* [Apollo Server authentication and authorization](https://www.apollographql.com/docs/apollo-server/security/authentication)
