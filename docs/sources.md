# Sources and scope

The skill draws on these primary sources:

- [GraphQL execution](https://graphql.org/learn/execution/)
- [GraphQL specification](https://spec.graphql.org/September2025/)
- [DataLoader](https://github.com/graphql/dataloader)
- [Apollo Server data fetching](https://www.apollographql.com/docs/apollo-server/data/fetching-data)
- [Apollo security overview](https://www.apollographql.com/docs/graphos/platform/security/overview)
- [Apollo demand oriented schema design](https://www.apollographql.com/docs/graphos/schema-design/guides/demand-oriented-schema-design)
- [Apollo authentication and authorization](https://www.apollographql.com/docs/apollo-server/security/authentication)

The specification defines GraphQL execution behavior. Library documentation defines that library's contract. Recommendations about application architecture, batching choices, and cache scope are guidance that must be checked against the target codebase and runtime.

The skill has examples in TypeScript, but its principles apply across server implementations. DataLoader is a JavaScript library. Other languages need an equivalent request scoped batching abstraction with the contract documented by that implementation. The project has not yet been evaluated across multiple language implementations.
