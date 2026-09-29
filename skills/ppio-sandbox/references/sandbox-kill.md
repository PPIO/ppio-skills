# Kill a sandbox

Killing shuts a sandbox down and **permanently removes** it — it cannot be resumed or reconnected.

## Kill a connected sandbox

**Python**
```python
sandbox = ppio.sandbox.create()
# ... use it ...
sandbox.kill()
```

**JavaScript / TypeScript**
```typescript
const sandbox = await ppio.sandbox.create()
// ... use it ...
await sandbox.kill()
```

## Kill by ID

Use the namespace method when you only have the ID. Returns `true`/`True` if found and killed, `false`/`False` if no sandbox with that ID exists.

**Python**
```python
killed = ppio.sandbox.kill("sbx-123")
if not killed:
    print("Sandbox not found")
```

**JavaScript / TypeScript**
```typescript
const killed = await ppio.sandbox.kill('sbx-123')
if (!killed) {
  console.log('Sandbox not found')
}
```

**CLI**
```bash
# Kill by ID (alias: sandbox kl)
ppio sandbox kill <sandboxID>
```

## Kill all (CLI)

```bash
ppio sandbox kill -a                     # all running
ppio sandbox kill -a --state running     # filter by state
ppio sandbox kill -a --metadata env=test # filter by metadata
```

Related: [pause-resume.md](sandbox-pause-resume.md) (kill vs. pause) · [list.md](sandbox-list.md)


## CLI parameters

### `ppio sandbox kill [sandboxIDs...]` (`kl`)

| Parameter | Required / default | Purpose |
|---|---|---|
| `[sandboxIDs...]` | required unless `--all` | One or more sandboxes to kill. |
| `-a, --all` | false | Kill all matching sandboxes. |
| `-s, --state <state>` | `running` with `--all` | Comma-separated state filter. |
| `-m, --metadata <metadata>` | unset with `--all` | Metadata filter. |
| `-y, --yes` | required for non-interactive/AI use | Skip destructive confirmation. |
| `-o, --output`, `-f, --format`, `--json` | pretty / deprecated | Output format. |

## SDK parameter tables

### `ppio.sandbox.kill(id, ...)` / `sandbox.kill(...)`

| Python parameter | JS parameter | Type | Required / default | Purpose |
|---|---|---|---|---|
| `sandbox_id` | `sandboxId` | string | Required for namespace call; omit on instance | Sandbox ID, not template name. |
| `**opts` | `opts` fields | Connection options | Optional | [Sandbox API options](common-parameters.md#sandbox-api-options); inherited client settings unless overridden. |
