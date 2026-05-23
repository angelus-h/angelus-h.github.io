# GraphQL Quick Reference

**Target Audience:** Backend Developers, Frontend Developers, Full-Stack Engineers, API Designers
**Prerequisites:** Basic understanding of APIs, HTTP, JSON, and at least one programming language (JavaScript/Python/Go recommended)

---

## Learning Path Overview

```
Week 1: Fundamentals → Week 2: Schema & Resolvers → Week 3: Advanced Patterns → Week 4: Production & Best Practices
```

---

## Week 1: GraphQL Fundamentals

### Day 1-2: Introduction to GraphQL

**Concepts:**
- What is GraphQL? (Query language for APIs)
- GraphQL vs REST API comparison
- Why use GraphQL? (Advantages and trade-offs)
- GraphQL ecosystem and tools
- Use cases and when NOT to use GraphQL

**Key Differences: GraphQL vs REST:**

| Aspect | REST | GraphQL |
|--------|------|---------|
| Endpoints | Multiple endpoints | Single endpoint |
| Data Fetching | Over-fetching/Under-fetching | Exact data requested |
| Versioning | /v1, /v2 endpoints | Schema evolution |
| Documentation | Swagger/OpenAPI | Self-documenting |
| Caching | HTTP caching | Requires custom solution |

**Hands-On:**
- Explore public GraphQL APIs (GitHub GraphQL API, SpaceX API)
- Use GraphiQL playground to run queries
- Compare equivalent REST vs GraphQL requests

**Resources:**
- [GraphQL Official Docs](https://graphql.org/learn/)
- [How to GraphQL Tutorial](https://www.howtographql.com/)
- [GitHub GraphQL API Explorer](https://docs.github.com/en/graphql/overview/explorer)

**Example - GitHub API:**
```graphql
# GraphQL - Get exactly what you need
query {
  viewer {
    login
    name
    repositories(first: 5) {
      nodes {
        name
        stargazerCount
      }
    }
  }
}

# vs REST - Multiple requests needed
# GET /user
# GET /user/repos
```

---

### Day 3-4: GraphQL Core Concepts

**Concepts:**
1. **Schema:** Contract between client and server
2. **Types:** Object types, Scalar types, Enums, Interfaces, Unions
3. **Queries:** Read operations
4. **Mutations:** Write operations
5. **Subscriptions:** Real-time updates
6. **Resolvers:** Functions that fetch data

**GraphQL Type System:**
```graphql
# Scalar Types (built-in)
Int       # Signed 32-bit integer
Float     # Signed double-precision floating-point value
String    # UTF-8 character sequence
Boolean   # true or false
ID        # Unique identifier (serialized as String)

# Object Type
type User {
  id: ID!              # ! means non-nullable
  name: String!
  email: String
  age: Int
  posts: [Post!]!      # Array of Posts
}

# Enum Type
enum Role {
  ADMIN
  USER
  GUEST
}

# Interface
interface Node {
  id: ID!
}

# Union
union SearchResult = User | Post | Comment
```

**Hands-On:**
- Design a simple schema (blog, e-commerce, social network)
- Understand nullable vs non-nullable fields
- Practice reading schema documentation

---

### Day 5-6: Queries and Mutations

**Queries:**
Reading data from the server.

```graphql
# Basic Query
query GetUser {
  user(id: "123") {
    id
    name
    email
  }
}

# Query with Variables
query GetUser($userId: ID!) {
  user(id: $userId) {
    id
    name
    email
  }
}

# Nested Queries
query GetUserWithPosts {
  user(id: "123") {
    id
    name
    posts {
      id
      title
      content
      comments {
        id
        text
        author {
          name
        }
      }
    }
  }
}

# Aliases (fetch same field with different arguments)
query {
  user1: user(id: "1") {
    name
  }
  user2: user(id: "2") {
    name
  }
}

# Fragments (reusable units)
fragment UserFields on User {
  id
  name
  email
}

query {
  user1: user(id: "1") {
    ...UserFields
  }
  user2: user(id: "2") {
    ...UserFields
  }
}
```

**Mutations:**
Creating, updating, or deleting data.

```graphql
# Create
mutation CreateUser($input: CreateUserInput!) {
  createUser(input: $input) {
    user {
      id
      name
      email
    }
    errors {
      field
      message
    }
  }
}

# Update
mutation UpdateUser($id: ID!, $input: UpdateUserInput!) {
  updateUser(id: $id, input: $input) {
    user {
      id
      name
    }
  }
}

# Delete
mutation DeleteUser($id: ID!) {
  deleteUser(id: $id) {
    success
    message
  }
}

# Multiple mutations in one request
mutation {
  createUser(input: {name: "Alice", email: "alice@example.com"}) {
    user {
      id
    }
  }
  createPost(input: {title: "Hello World", authorId: "123"}) {
    post {
      id
    }
  }
}
```

**Hands-On:**
- Write queries for different scenarios
- Practice using variables
- Create mutations for CRUD operations
- Use fragments to avoid repetition

---

### Day 7: GraphQL Clients

**Concepts:**
- Simple HTTP clients (fetch, axios)
- Dedicated GraphQL clients (Apollo Client, urql, Relay)
- Client-side caching
- Optimistic updates

**Simple HTTP Request (JavaScript):**
```javascript
const query = `
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
      email
    }
  }
`;

const variables = { id: "123" };

fetch('https://api.example.com/graphql', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    query,
    variables,
  }),
})
  .then(res => res.json())
  .then(data => console.log(data));
```

**Apollo Client (React):**
```javascript
import { ApolloClient, InMemoryCache, gql, useQuery } from '@apollo/client';

const client = new ApolloClient({
  uri: 'https://api.example.com/graphql',
  cache: new InMemoryCache(),
});

const GET_USER = gql`
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
      email
    }
  }
`;

function UserProfile({ userId }) {
  const { loading, error, data } = useQuery(GET_USER, {
    variables: { id: userId },
  });

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h1>{data.user.name}</h1>
      <p>{data.user.email}</p>
    </div>
  );
}
```

**Hands-On:**
- Make GraphQL requests with plain fetch
- Setup Apollo Client in a React app
- Implement queries and mutations
- Explore client-side caching

---

## Week 2: Schema Design & Resolvers

### Day 8-9: Schema Definition Language (SDL)

**Concepts:**
- Schema-first vs code-first approach
- Type definitions
- Input types
- Custom scalars
- Directives

**Complete Schema Example:**
```graphql
# Custom Scalar
scalar DateTime

# Enums
enum PostStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}

# Interfaces
interface Node {
  id: ID!
  createdAt: DateTime!
  updatedAt: DateTime!
}

# Types
type User implements Node {
  id: ID!
  createdAt: DateTime!
  updatedAt: DateTime!
  name: String!
  email: String!
  bio: String
  avatar: String
  role: Role!
  posts: [Post!]!
  followers: [User!]!
  following: [User!]!
}

type Post implements Node {
  id: ID!
  createdAt: DateTime!
  updatedAt: DateTime!
  title: String!
  content: String!
  status: PostStatus!
  author: User!
  comments: [Comment!]!
  tags: [String!]!
}

type Comment implements Node {
  id: ID!
  createdAt: DateTime!
  updatedAt: DateTime!
  text: String!
  author: User!
  post: Post!
}

# Input Types
input CreateUserInput {
  name: String!
  email: String!
  bio: String
  avatar: String
}

input UpdatePostInput {
  title: String
  content: String
  status: PostStatus
  tags: [String!]
}

# Root Types
type Query {
  # User queries
  user(id: ID!): User
  users(first: Int, after: String): UserConnection!
  me: User

  # Post queries
  post(id: ID!): Post
  posts(
    first: Int
    after: String
    status: PostStatus
    authorId: ID
  ): PostConnection!

  # Search
  search(query: String!): [SearchResult!]!
}

type Mutation {
  # User mutations
  createUser(input: CreateUserInput!): CreateUserPayload!
  updateUser(id: ID!, input: UpdateUserInput!): UpdateUserPayload!
  deleteUser(id: ID!): DeleteUserPayload!

  # Post mutations
  createPost(input: CreatePostInput!): CreatePostPayload!
  updatePost(id: ID!, input: UpdatePostInput!): UpdatePostPayload!
  deletePost(id: ID!): DeletePostPayload!

  # Follow mutations
  followUser(userId: ID!): FollowUserPayload!
  unfollowUser(userId: ID!): UnfollowUserPayload!
}

type Subscription {
  postAdded: Post!
  commentAdded(postId: ID!): Comment!
  userUpdated(userId: ID!): User!
}

# Payload Types (best practice)
type CreateUserPayload {
  user: User
  errors: [Error!]
}

type Error {
  field: String
  message: String!
}

# Connection Types (Relay-style pagination)
type UserConnection {
  edges: [UserEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type UserEdge {
  cursor: String!
  node: User!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}

# Union Type
union SearchResult = User | Post | Comment

# Directives
directive @auth(requires: Role = USER) on FIELD_DEFINITION
directive @deprecated(reason: String) on FIELD_DEFINITION
```

**Hands-On:**
- Design a schema for your domain (e-commerce, social, CMS)
- Define all types, inputs, and root operations
- Use interfaces for shared fields
- Implement pagination with connections

---

### Day 10-11: Resolvers

**Concepts:**
- What are resolvers?
- Resolver function signature
- Parent, args, context, info parameters
- Resolver chains
- Data sources (database, REST API, microservices)

**Resolver Function Signature:**
```javascript
fieldName(parent, args, context, info) {
  // parent: Result from parent resolver
  // args: Arguments passed to the field
  // context: Shared context (auth, dataloaders, etc.)
  // info: Query AST and schema info

  return value;
}
```

**Example Resolvers (Node.js with Apollo Server):**
```javascript
const resolvers = {
  Query: {
    // Get user by ID
    user: async (parent, { id }, context) => {
      return await context.dataSources.userAPI.getUserById(id);
    },

    // Get current user (authenticated)
    me: async (parent, args, context) => {
      if (!context.user) {
        throw new Error('Not authenticated');
      }
      return await context.dataSources.userAPI.getUserById(context.user.id);
    },

    // Get paginated posts
    posts: async (parent, { first, after, status }, context) => {
      return await context.dataSources.postAPI.getPosts({
        limit: first,
        cursor: after,
        status,
      });
    },
  },

  Mutation: {
    createUser: async (parent, { input }, context) => {
      try {
        const user = await context.dataSources.userAPI.createUser(input);
        return { user, errors: null };
      } catch (error) {
        return {
          user: null,
          errors: [{ field: 'general', message: error.message }],
        };
      }
    },

    updatePost: async (parent, { id, input }, context) => {
      // Check authorization
      const post = await context.dataSources.postAPI.getPostById(id);
      if (post.authorId !== context.user.id) {
        throw new Error('Not authorized');
      }

      return await context.dataSources.postAPI.updatePost(id, input);
    },
  },

  // Type resolvers (nested fields)
  User: {
    posts: async (parent, args, context) => {
      // parent.id is available from the User object
      return await context.dataSources.postAPI.getPostsByAuthorId(parent.id);
    },

    followers: async (parent, args, context) => {
      return await context.dataSources.userAPI.getFollowers(parent.id);
    },
  },

  Post: {
    author: async (parent, args, context) => {
      // Use DataLoader to avoid N+1 problem
      return await context.loaders.userLoader.load(parent.authorId);
    },

    comments: async (parent, args, context) => {
      return await context.dataSources.commentAPI.getCommentsByPostId(parent.id);
    },
  },

  // Union type resolver
  SearchResult: {
    __resolveType(obj, context, info) {
      if (obj.email) return 'User';
      if (obj.title) return 'Post';
      if (obj.text) return 'Comment';
      return null;
    },
  },
};
```

**Hands-On:**
- Implement resolvers for your schema
- Connect to a database (PostgreSQL, MongoDB)
- Handle errors gracefully
- Implement authorization checks

---

### Day 12-13: Server Setup

**Apollo Server (Node.js):**
```javascript
const { ApolloServer } = require('@apollo/server');
const { startStandaloneServer } = require('@apollo/server/standalone');

// Type definitions
const typeDefs = `#graphql
  type User {
    id: ID!
    name: String!
    email: String!
  }

  type Query {
    users: [User!]!
    user(id: ID!): User
  }

  type Mutation {
    createUser(name: String!, email: String!): User!
  }
`;

// Resolvers
const resolvers = {
  Query: {
    users: () => users,
    user: (parent, { id }) => users.find(u => u.id === id),
  },
  Mutation: {
    createUser: (parent, { name, email }) => {
      const user = { id: String(users.length + 1), name, email };
      users.push(user);
      return user;
    },
  },
};

// Server setup
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

// Start server
const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 },
  context: async ({ req }) => {
    // Get auth token from headers
    const token = req.headers.authorization || '';

    // Get user from token
    const user = await getUserFromToken(token);

    // Return context object
    return {
      user,
      dataSources: {
        userAPI: new UserAPI(),
        postAPI: new PostAPI(),
      },
    };
  },
});

console.log(`Server ready at: ${url}`);
```

**GraphQL Yoga (Node.js - simpler alternative):**
```javascript
import { createServer } from '@graphql-yoga/node';

const server = createServer({
  schema: {
    typeDefs,
    resolvers,
  },
  context: ({ request }) => ({
    user: getUserFromRequest(request),
  }),
});

server.start();
```

**Hands-On:**
- Setup Apollo Server or GraphQL Yoga
- Implement your schema and resolvers
- Test with GraphiQL/Apollo Sandbox
- Add authentication middleware

---

### Day 14: Week 2 Practice

**Challenge:**
Build a complete GraphQL API for a blog platform:
- Schema: Users, Posts, Comments
- Queries: Get user, get posts, search
- Mutations: CRUD operations
- Authentication: JWT-based
- Database: PostgreSQL or MongoDB

---

## Week 3: Advanced Patterns

### Day 15-16: DataLoader (N+1 Problem Solution)

**The N+1 Problem:**
```javascript
// BAD: N+1 queries
const posts = await getPosts(); // 1 query

for (const post of posts) {
  const author = await getUser(post.authorId); // N queries
  post.author = author;
}
// Total: 1 + N queries
```

**DataLoader Solution:**
```javascript
const DataLoader = require('dataloader');

// Batch function: receives array of IDs, returns array of Users
async function batchUsers(ids) {
  const users = await db.users.findMany({
    where: { id: { in: ids } },
  });

  // Return users in same order as ids
  return ids.map(id => users.find(user => user.id === id));
}

// Create DataLoader
const userLoader = new DataLoader(batchUsers);

// Usage in resolver
const resolvers = {
  Post: {
    author: async (post, args, context) => {
      // DataLoader batches all requests in a single tick
      return await context.loaders.userLoader.load(post.authorId);
    },
  },
};

// Context setup
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: () => ({
    loaders: {
      userLoader: new DataLoader(batchUsers),
    },
  }),
});
```

**Hands-On:**
- Identify N+1 problems in your API
- Implement DataLoader for common relationships
- Measure performance improvement
- Handle cache invalidation

---

### Day 17-18: Subscriptions (Real-Time Updates)

**Concepts:**
- WebSocket protocol
- PubSub pattern
- Subscription resolvers
- Client subscriptions

**Server Setup:**
```javascript
const { ApolloServer } = require('@apollo/server');
const { createServer } = require('http');
const { ApolloServerPluginDrainHttpServer } = require('@apollo/server/plugin/drainHttpServer');
const { makeExecutableSchema } = require('@graphql-tools/schema');
const { WebSocketServer } = require('ws');
const { useServer } = require('graphql-ws/lib/use/ws');
const { PubSub } = require('graphql-subscriptions');

const pubsub = new PubSub();

const typeDefs = `#graphql
  type Message {
    id: ID!
    text: String!
    author: String!
  }

  type Query {
    messages: [Message!]!
  }

  type Mutation {
    sendMessage(text: String!, author: String!): Message!
  }

  type Subscription {
    messageAdded: Message!
  }
`;

const resolvers = {
  Query: {
    messages: () => messages,
  },
  Mutation: {
    sendMessage: (parent, { text, author }) => {
      const message = { id: String(messages.length + 1), text, author };
      messages.push(message);

      // Publish event
      pubsub.publish('MESSAGE_ADDED', { messageAdded: message });

      return message;
    },
  },
  Subscription: {
    messageAdded: {
      subscribe: () => pubsub.asyncIterator(['MESSAGE_ADDED']),
    },
  },
};

const schema = makeExecutableSchema({ typeDefs, resolvers });

// HTTP server
const httpServer = createServer();

// WebSocket server
const wsServer = new WebSocketServer({
  server: httpServer,
  path: '/graphql',
});

const serverCleanup = useServer({ schema }, wsServer);

// Apollo Server
const server = new ApolloServer({
  schema,
  plugins: [
    ApolloServerPluginDrainHttpServer({ httpServer }),
    {
      async serverWillStart() {
        return {
          async drainServer() {
            await serverCleanup.dispose();
          },
        };
      },
    },
  ],
});

await server.start();
```

**Client Subscription (React with Apollo Client):**
```javascript
import { useSubscription, gql } from '@apollo/client';

const MESSAGE_SUBSCRIPTION = gql`
  subscription OnMessageAdded {
    messageAdded {
      id
      text
      author
    }
  }
`;

function Messages() {
  const { data, loading } = useSubscription(MESSAGE_SUBSCRIPTION);

  if (loading) return <p>Loading...</p>;

  return (
    <div>
      <h2>New Message:</h2>
      <p>{data.messageAdded.author}: {data.messageAdded.text}</p>
    </div>
  );
}
```

**Hands-On:**
- Setup WebSocket server
- Implement subscriptions (chat, notifications, live updates)
- Test with Apollo Client
- Handle reconnections

---

### Day 19-20: Pagination

**Offset-based Pagination:**
```graphql
type Query {
  posts(offset: Int, limit: Int): [Post!]!
}
```

**Cursor-based Pagination (Relay Connection Pattern):**
```graphql
type Query {
  posts(first: Int, after: String, last: Int, before: String): PostConnection!
}

type PostConnection {
  edges: [PostEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type PostEdge {
  cursor: String!
  node: Post!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}
```

**Resolver Implementation:**
```javascript
const resolvers = {
  Query: {
    posts: async (parent, { first, after }, context) => {
      const limit = first || 10;
      const offset = after ? parseInt(Buffer.from(after, 'base64').toString()) : 0;

      const posts = await db.posts.findMany({
        skip: offset,
        take: limit + 1, // Fetch one extra to check if there's a next page
      });

      const hasNextPage = posts.length > limit;
      const edges = posts.slice(0, limit).map((post, index) => ({
        cursor: Buffer.from(String(offset + index)).toString('base64'),
        node: post,
      }));

      return {
        edges,
        pageInfo: {
          hasNextPage,
          hasPreviousPage: offset > 0,
          startCursor: edges[0]?.cursor,
          endCursor: edges[edges.length - 1]?.cursor,
        },
        totalCount: await db.posts.count(),
      };
    },
  },
};
```

**Hands-On:**
- Implement both pagination strategies
- Choose appropriate strategy for your use case
- Handle edge cases (empty results, invalid cursors)

---

### Day 21: Authorization & Authentication

**Authentication Patterns:**
1. **JWT in Headers:**
```javascript
context: async ({ req }) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  const user = await verifyToken(token);
  return { user };
}
```

2. **Cookie-based:**
```javascript
context: async ({ req }) => {
  const sessionId = req.cookies.sessionId;
  const user = await getSessionUser(sessionId);
  return { user };
}
```

**Authorization Strategies:**

**1. Resolver-level:**
```javascript
const resolvers = {
  Mutation: {
    deletePost: async (parent, { id }, context) => {
      if (!context.user) {
        throw new Error('Not authenticated');
      }

      const post = await getPost(id);
      if (post.authorId !== context.user.id && context.user.role !== 'ADMIN') {
        throw new Error('Not authorized');
      }

      return await deletePost(id);
    },
  },
};
```

**2. Directive-based:**
```graphql
directive @auth(requires: Role = USER) on FIELD_DEFINITION

type Mutation {
  deletePost(id: ID!): Boolean! @auth(requires: ADMIN)
}
```

```javascript
const { mapSchema, getDirective, MapperKind } = require('@graphql-tools/utils');

function authDirective(schema, directiveName) {
  return mapSchema(schema, {
    [MapperKind.OBJECT_FIELD]: (fieldConfig) => {
      const authDirective = getDirective(schema, fieldConfig, directiveName)?.[0];

      if (authDirective) {
        const { requires } = authDirective;
        const { resolve = defaultFieldResolver } = fieldConfig;

        fieldConfig.resolve = async function(source, args, context, info) {
          if (!context.user) {
            throw new Error('Not authenticated');
          }

          if (requires && context.user.role !== requires) {
            throw new Error('Not authorized');
          }

          return resolve(source, args, context, info);
        };
      }

      return fieldConfig;
    },
  });
}
```

**Hands-On:**
- Implement JWT authentication
- Add role-based authorization
- Create auth directives
- Handle token refresh

---

## Week 4: Production & Best Practices

### Day 22-23: Error Handling

**Error Types:**
1. **Syntax Errors:** Invalid GraphQL syntax
2. **Validation Errors:** Query doesn't match schema
3. **Execution Errors:** Resolver throws error
4. **Custom Errors:** Business logic errors

**Custom Error Classes:**
```javascript
const { GraphQLError } = require('graphql');

class AuthenticationError extends GraphQLError {
  constructor(message) {
    super(message, {
      extensions: {
        code: 'UNAUTHENTICATED',
        http: { status: 401 },
      },
    });
  }
}

class ValidationError extends GraphQLError {
  constructor(message, field) {
    super(message, {
      extensions: {
        code: 'BAD_USER_INPUT',
        field,
        http: { status: 400 },
      },
    });
  }
}

// Usage
const resolvers = {
  Mutation: {
    createUser: async (parent, { input }, context) => {
      if (!isValidEmail(input.email)) {
        throw new ValidationError('Invalid email format', 'email');
      }

      if (!context.user) {
        throw new AuthenticationError('Must be logged in');
      }

      // ... create user
    },
  },
};
```

**Error Response Format:**
```json
{
  "errors": [
    {
      "message": "Invalid email format",
      "locations": [{ "line": 2, "column": 3 }],
      "path": ["createUser"],
      "extensions": {
        "code": "BAD_USER_INPUT",
        "field": "email"
      }
    }
  ],
  "data": null
}
```

**Hands-On:**
- Create custom error classes
- Implement proper error handling in resolvers
- Return user-friendly error messages
- Log errors for debugging

---

### Day 24-25: Performance Optimization

**Best Practices:**

**1. Query Complexity Analysis:**
```javascript
const { createComplexityLimitRule } = require('graphql-validation-complexity');

const server = new ApolloServer({
  typeDefs,
  resolvers,
  validationRules: [
    createComplexityLimitRule(1000, {
      onCost: (cost) => console.log('Query cost:', cost),
    }),
  ],
});
```

**2. Query Depth Limiting:**
```javascript
const depthLimit = require('graphql-depth-limit');

const server = new ApolloServer({
  typeDefs,
  resolvers,
  validationRules: [depthLimit(10)],
});
```

**3. Persisted Queries:**
```javascript
// Client sends query hash instead of full query
{
  "extensions": {
    "persistedQuery": {
      "version": 1,
      "sha256Hash": "ecf4edb46db40b5132295c0291d62fb65d6759a9eedfa4d5d612dd5ec54a6b38"
    }
  }
}
```

**4. Field-level Caching:**
```javascript
const { cacheControl } = require('@apollo/server-plugin-response-cache');

const typeDefs = `#graphql
  type Query {
    user(id: ID!): User @cacheControl(maxAge: 60)
    posts: [Post!]! @cacheControl(maxAge: 30)
  }
`;
```

**5. DataLoader for Batching:**
Already covered in Day 15-16.

**Hands-On:**
- Implement query complexity limits
- Add response caching
- Profile slow queries
- Optimize database queries

---

### Day 26-27: Testing

**Unit Testing Resolvers:**
```javascript
const { user } = require('./resolvers');

describe('User resolver', () => {
  it('should return user by id', async () => {
    const mockContext = {
      dataSources: {
        userAPI: {
          getUserById: jest.fn(() => ({ id: '1', name: 'Alice' })),
        },
      },
    };

    const result = await user(null, { id: '1' }, mockContext);

    expect(result).toEqual({ id: '1', name: 'Alice' });
    expect(mockContext.dataSources.userAPI.getUserById).toHaveBeenCalledWith('1');
  });
});
```

**Integration Testing:**
```javascript
const { ApolloServer } = require('@apollo/server');
const { typeDefs, resolvers } = require('./schema');

describe('GraphQL API', () => {
  let server;

  beforeAll(() => {
    server = new ApolloServer({ typeDefs, resolvers });
  });

  it('should create a user', async () => {
    const result = await server.executeOperation({
      query: `
        mutation CreateUser($input: CreateUserInput!) {
          createUser(input: $input) {
            user {
              id
              name
            }
          }
        }
      `,
      variables: {
        input: { name: 'Alice', email: 'alice@example.com' },
      },
    });

    expect(result.errors).toBeUndefined();
    expect(result.data.createUser.user.name).toBe('Alice');
  });
});
```

**End-to-End Testing:**
```javascript
const request = require('supertest');
const app = require('./app');

describe('GraphQL E2E', () => {
  it('should authenticate and fetch user', async () => {
    // Login
    const loginRes = await request(app)
      .post('/graphql')
      .send({
        query: `
          mutation {
            login(email: "alice@example.com", password: "secret") {
              token
            }
          }
        `,
      });

    const token = loginRes.body.data.login.token;

    // Fetch user
    const userRes = await request(app)
      .post('/graphql')
      .set('Authorization', `Bearer ${token}`)
      .send({
        query: `
          query {
            me {
              id
              name
            }
          }
        `,
      });

    expect(userRes.body.data.me.name).toBe('Alice');
  });
});
```

**Hands-On:**
- Write unit tests for resolvers
- Create integration tests for queries/mutations
- Setup E2E tests
- Achieve >80% code coverage

---

### Day 28: Production Deployment

**Deployment Checklist:**
- [ ] Enable production mode
- [ ] Disable introspection in production
- [ ] Setup CORS properly
- [ ] Add rate limiting
- [ ] Enable HTTPS
- [ ] Setup monitoring and logging
- [ ] Configure caching
- [ ] Add health check endpoint

**Apollo Server Production Config:**
```javascript
const server = new ApolloServer({
  typeDefs,
  resolvers,
  introspection: process.env.NODE_ENV !== 'production',
  plugins: [
    ApolloServerPluginLandingPageDisabled(),
    // Or: ApolloServerPluginLandingPageProductionDefault()
  ],
  formatError: (error) => {
    // Don't expose internal errors
    if (error.extensions?.code === 'INTERNAL_SERVER_ERROR') {
      return new GraphQLError('Internal server error', {
        extensions: { code: 'INTERNAL_SERVER_ERROR' },
      });
    }
    return error;
  },
});
```

**Rate Limiting:**
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
});

app.use('/graphql', limiter);
```

**Monitoring:**
```javascript
const { ApolloServerPluginUsageReporting } = require('@apollo/server/plugin/usageReporting');

const server = new ApolloServer({
  typeDefs,
  resolvers,
  plugins: [
    ApolloServerPluginUsageReporting({
      sendVariableValues: { all: true },
      sendHeaders: { all: true },
    }),
  ],
});
```

**Hands-On:**
- Deploy to production (Heroku, AWS, Vercel)
- Setup Apollo Studio for monitoring
- Configure environment variables
- Test in production

---

## Advanced Topics

### Federation (Microservices)

**Concept:** Split GraphQL schema across multiple services.

```graphql
# Users Service
type User @key(fields: "id") {
  id: ID!
  name: String!
  email: String!
}

# Posts Service
extend type User @key(fields: "id") {
  id: ID! @external
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  author: User!
}
```

### Code Generation

**GraphQL Code Generator:**
```bash
npm install -D @graphql-codegen/cli @graphql-codegen/typescript

# codegen.yml
schema: http://localhost:4000/graphql
generates:
  ./src/types.ts:
    plugins:
      - typescript
      - typescript-resolvers
```

### Schema Stitching

Combine multiple GraphQL schemas into one.

### Custom Directives

Create reusable schema directives (@deprecated, @auth, @cacheControl, etc.).

---

## Real-World Project Ideas

**1. Social Media API**
- Users, posts, comments, likes
- Real-time notifications (subscriptions)
- Image upload
- Search functionality

**2. E-commerce Platform**
- Products, categories, cart, orders
- Payment integration
- Inventory management
- Reviews and ratings

**3. Task Management System**
- Projects, tasks, subtasks
- Team collaboration
- Real-time updates
- File attachments

**4. Blogging Platform**
- Posts, authors, tags, categories
- Draft/publish workflow
- SEO metadata
- Analytics integration

---

## Tools & Resources

**Development Tools:**
- [GraphiQL](https://github.com/graphql/graphiql) - In-browser IDE
- [Apollo Studio](https://studio.apollographql.com/) - Schema registry, monitoring
- [Postman](https://www.postman.com/) - GraphQL request testing
- [Altair GraphQL Client](https://altair.sirmuel.design/) - Feature-rich client

**Libraries:**
- **Server:** Apollo Server, GraphQL Yoga, Mercurius, Express GraphQL
- **Client:** Apollo Client, urql, Relay, graphql-request
- **Tools:** GraphQL Code Generator, DataLoader, graphql-tools

**Learning Resources:**
- [Official GraphQL Docs](https://graphql.org/)
- [How to GraphQL](https://www.howtographql.com/)
- [Apollo Docs](https://www.apollographql.com/docs/)
- [GraphQL Weekly Newsletter](https://www.graphqlweekly.com/)

---

## Success Criteria

By completing this learning plan, you should be able to:

- [ ] Explain GraphQL fundamentals and benefits
- [ ] Design effective GraphQL schemas
- [ ] Implement queries, mutations, and subscriptions
- [ ] Build a GraphQL server (Apollo/Yoga)
- [ ] Solve N+1 problem with DataLoader
- [ ] Implement authentication and authorization
- [ ] Handle errors properly
- [ ] Optimize performance
- [ ] Write tests for GraphQL APIs
- [ ] Deploy to production
- [ ] Integrate GraphQL with frontend frameworks

---

**Last Updated:** 2026-03-13
**Maintainer:** Miklos Greczi
