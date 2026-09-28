# List sandboxes

`ppio.sandbox.list(...)` returns a **paginator**. Pull pages with `next_items()` (Python) / `nextItems()` (JS); check `has_next` / `hasNext` for more. Without a query it lists running and paused sandboxes.

## Basic + read fields

**Python**
```python
from ppio_sandbox import PPIO, SandboxQuery, SandboxState

ppio = PPIO()

paginator = ppio.sandbox.list(query=SandboxQuery(state=[SandboxState.RUNNING]))
running = paginator.next_items()

s = running[0]
print(s.sandbox_id, s.state, s.template_id, s.metadata, s.started_at, s.end_at)
```

**JavaScript / TypeScript**
```typescript
import { PPIO, SandboxState } from 'ppio-sandbox'

const ppio = new PPIO()

const paginator = await ppio.sandbox.list({ query: { state: [SandboxState.RUNNING] } })
const running = await paginator.nextItems()

const s = running[0]
console.log(s.sandboxId, s.state, s.templateId, s.metadata, s.startedAt, s.endAt)
```

**CLI**
```bash
ppio sandbox list                        # running only (alias: sandbox ls)
ppio sandbox list --metadata key1=value1 # filter by metadata
ppio sandbox list --output json          # JSON output
ppio sandbox list --output yaml          # YAML output
```
> `-o/--output` values: `pretty` (default), `json`, `yaml`. The old `--format json` and `--json` are deprecated aliases.

## Filter by state

`state` accepts `running`, `paused`, or both.

**Python**
```python
paginator = ppio.sandbox.list(
    query=SandboxQuery(state=[SandboxState.RUNNING, SandboxState.PAUSED]),
)
```

**JavaScript / TypeScript**
```typescript
const paginator = await ppio.sandbox.list({
  query: { state: [SandboxState.RUNNING, SandboxState.PAUSED] },
})
```

## Filter by metadata

Multiple key/value pairs are AND-ed; filtering happens server-side.

**Python**
```python
paginator = ppio.sandbox.list(
    query=SandboxQuery(metadata={"user_id": "123", "env": "dev"}),
)
for s in paginator.next_items():
    print(s.sandbox_id, s.metadata)
```

**JavaScript / TypeScript**
```typescript
const paginator = await ppio.sandbox.list({
  query: { metadata: { userId: '123', env: 'dev' } },
})
for (const s of await paginator.nextItems()) {
  console.log(s.sandboxId, s.metadata)
}
```

## Pagination

```python
paginator = ppio.sandbox.list()
first_page = paginator.next_items()
if paginator.has_next:
    next_page = paginator.next_items()
```

```typescript
const paginator = await ppio.sandbox.list()
const firstPage = await paginator.nextItems()
if (paginator.hasNext) {
  const nextPage = await paginator.nextItems()
}
```

Related: [connect.md](sandbox-connect.md) — use a listed `sandbox_id` to reconnect.


## CLI parameters

### `ppio sandbox list` (`ls`)

| Parameter | Required / default | Purpose |
|---|---|---|
| `--all` | false | Fetch all pages. No `-a` short form here (unlike `sandbox kill`). |
| `-s, --state <state>` | `running` | Comma-separated state filter (`running`, `paused`). |
| `-p, --page <page>` | `1` | One-based CLI page. |
| `-m, --metadata <metadata>` | unset | Metadata filter, e.g. `env=test`. |
| `-l, --limit <limit>` | `20`; `0` means no limit | Entries per CLI page. |
| `-o, --output`, `-f, --format`, `--json` | pretty / deprecated | Output format. |

## SDK parameter tables

### `ppio.sandbox.list(...)`

| Python parameter | JS parameter | Type | Required / default | Purpose |
|---|---|---|---|---|
| `query` | `query` | object / SandboxQuery | Unset | Filter object, fields below. |
| `query.metadata` | `query.metadata` | string map | Unset | Match all provided label key/value pairs. |
| `query.state` | `query.state` | state array | Server: running and paused | Values: running, paused, cloning, commiting (API spelling), snapshotting. |
| `limit` | `limit` | integer | 100 | Maximum entries per API page; not CLI display limit. |
| `next_token` | `nextToken` | string | Unset | Resume from an API continuation token. |
| `**opts` | `opts` fields | Connection options | Optional | [Sandbox API options](common-parameters.md#sandbox-api-options); inherited client settings unless overridden. |

### `paginator.next_items()` / `paginator.nextItems()`

Fetch the next page; check `has_next` / `hasNext` first. These are properties, not callable methods.

| Python parameter | JS parameter | Type | Required / default | Purpose |
|---|---|---|---|---|
| — | — | — | No parameters | Uses the state of this object; no options argument. |
