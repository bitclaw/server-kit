# @bitclaw/server-kit

Small, dependency-light server-side utilities shared across bitclaw projects.
Not tied to any specific database, framework, or runtime beyond plain
JS/TypeScript , if a utility needs `bun:sqlite` or another runtime-specific
dependency, it belongs in a more specific package, not here.

## Install

```sh
npm install @bitclaw/server-kit
```

## `TTLCache`

In-memory TTL cache backed by a `Map`. Designed for server-side
deduplication of expensive lookups (auth sessions, membership checks,
bootstrap data) across HTTP requests. Entries expire after `ttl`
milliseconds and are automatically pruned when the map exceeds `maxSize`.

```ts
import { TTLCache } from '@bitclaw/server-kit/ttl-cache';

type BootstrapData = { user: User; workspaces: Workspace[] };

const bootstrapCache = new TTLCache<BootstrapData>({ ttl: 30_000 });

// In your server function:
const cached = bootstrapCache.get(sessionId);
if (cached) return cached;

const data = await expensiveQuery();
bootstrapCache.set(sessionId, data);
return data;
```

Previously shipped as `@bitclaw/sqlite/ttl-cache` , extracted here because
it has zero SQLite coupling. `@bitclaw/sqlite@2.0.0+` no longer exports it;
update the import path if migrating from an older version.

## Development

```sh
bun install
bun test
bun run typecheck
bun run lint
bun run build
```
