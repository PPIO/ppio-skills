# Idle timeout

Automatically stop or pause a sandbox when no active connections are detected for a while. Configured through the **`metadata`** field at creation: key `idle_timeout`, value = seconds **as a string**.

This differs from [timeout.md](sandbox-timeout.md) (the hard max lifetime): idle timeout only fires when the sandbox has been idle (no client connected).

Idle timeout applies even on a [long-running](sandbox-long-running.md) sandbox — a sandbox with a 7-day `timeout` is still stopped after `idle_timeout` seconds of inactivity. Set both keys together when you want both behaviors.

## Basic — auto-kill after inactivity

**Python**
```python
# Killed after 60 seconds of inactivity
sandbox = ppio.sandbox.create(metadata={"idle_timeout": "60"})
```

**JavaScript / TypeScript**
```typescript
// Killed after 60 seconds of inactivity
const sandbox = await ppio.sandbox.create({ metadata: { idle_timeout: '60' } })
```

## Pause instead of kill

Pass `lifecycle` with `on_timeout` / `onTimeout` set to `pause` so the sandbox is paused (resumable) instead of killed. Add `auto_resume` / `autoResume` to bring it back on the next activity.

**Python**
```python
sandbox = ppio.sandbox.create(
    metadata={"idle_timeout": "60"},
    lifecycle={
        "on_timeout": "pause",
        "auto_resume": True,
    },
)
# After 60s of inactivity, the sandbox is paused.
```

**JavaScript / TypeScript**
```typescript
const sandbox = await ppio.sandbox.create({
  metadata: { idle_timeout: '60' },
  lifecycle: {
    onTimeout: 'pause',
    autoResume: true,
  },
})
// After 60s of inactivity, the sandbox is paused.
```

> The older `auto_pause` / `autoPause` flag is deprecated. Python `create` still accepts it; in JS it exists only on `betaCreate`, so passing it to `create` has no effect. Use `lifecycle` in both languages.

## Combine with other metadata

`idle_timeout` is just one metadata key; keep your own alongside it.

**Python**
```python
sandbox = ppio.sandbox.create(
    metadata={"idle_timeout": "120", "env": "production", "user_id": "user-123"},
)
```

**JavaScript / TypeScript**
```typescript
const sandbox = await ppio.sandbox.create({
  metadata: { idle_timeout: '120', env: 'production', userId: 'user-123' },
})
```

## Disable

Omit `idle_timeout`, or set it to `"0"` — the sandbox then runs until its maximum lifetime.

```python
sandbox = ppio.sandbox.create(metadata={"idle_timeout": "0"})
sandbox2 = ppio.sandbox.create()  # or just omit it
```

```typescript
const sandbox = await ppio.sandbox.create({ metadata: { idle_timeout: '0' } })
const sandbox2 = await ppio.sandbox.create() // or just omit it
```

Related: [timeout.md](sandbox-timeout.md) · [long-running.md](sandbox-long-running.md) · [pause-resume.md](sandbox-pause-resume.md)

## SDK parameter table

| Python / JS parameter | Type | Required / default | Purpose |
|---|---|---|---|
| `metadata["idle_timeout"]` | string seconds | unset | Idle period after which an inactive sandbox is stopped or paused. |
| `lifecycle.on_timeout` / `lifecycle.onTimeout` | `"kill"` or `"pause"` | `kill` | Pause instead of kill when the timeout fires. |
| `lifecycle.auto_resume` / `lifecycle.autoResume` | boolean | false | Resume on next activity; valid only with `pause`. |
| `auto_pause` / `autoPause` | boolean | false; deprecated | Superseded by `lifecycle`. Python `create` accepts it; JS only on `betaCreate`. |
