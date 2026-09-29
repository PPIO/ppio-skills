# Regions

PPIO Sandbox is served from two regions. The region determines the **API domain** the SDK, CLI, and sandboxes use — it is configured at connection time, not per-sandbox.

| Region | Version | Domain | Notes |
|--------|---------|--------|-------|
| `cn-shanghai-1` | v1 | `sandbox.ppio.cn` | **Default** used by the current SDK and CLI. Does not support Secrets or Snapshots. |
| `cn-beijing-1` | v2 | `cn-beijing-1.sandbox.ppio.com` | Recommended. Full feature set, including **Secrets** and **Snapshots**. Selected via the `PPIO_DOMAIN` environment variable. |

The v2 region (`cn-beijing-1`) is **recommended** because it adds Secrets and Snapshots on top of v1 — but it is **not** the default: the SDK and CLI default to v1 (`cn-shanghai-1`), so v2 must be selected explicitly. Both regions are supported by the documented SDK and CLI.

> **Legacy-domain limits:** features marked *modern domains only* (Secrets, Snapshots, Volumes) are **not available** on the v1 region. Since v1 is the default, these features fail unless `PPIO_DOMAIN` is set to the v2 domain. If a command fails with "not supported on legacy domains," the configured region is v1 — switch to v2 (`cn-beijing-1`) to use those features.

## Default (v1 / cn-shanghai-1)

No configuration needed — this is the SDK and CLI default.

```bash
# No region config required. The domain defaults to sandbox.ppio.cn.
ppio sandbox list
```

## Select the v2 region (recommended)

Set `PPIO_DOMAIN` to the v2 domain (`cn-beijing-1.sandbox.ppio.com`). This is the one thing a user needs to change to switch regions — it works for both the SDK and the CLI, and it is required for Secrets and Snapshots.

```bash
export PPIO_DOMAIN=cn-beijing-1.sandbox.ppio.com
ppio sandbox list
```

**Python**
```python
import os
os.environ["PPIO_DOMAIN"] = "cn-beijing-1.sandbox.ppio.com"
```

**JavaScript / TypeScript**
```typescript
process.env.PPIO_DOMAIN = 'cn-beijing-1.sandbox.ppio.com'
```

## Connectivity notes

- The **API token** is a credential stored in `~/.ppio/config.json`, independent of the region. The **domain** is a connection address. They are separate — see [cli-reference.md](cli-reference.md) for `auth login`/`auth configure`.
- The sandbox URL is derived from the same domain: `https://<port>-<sandboxID>.<domain>`.
- `PPIO_DOMAIN` is read at connection build time — set it before creating the SDK client / running the CLI command.

Related: [sandbox-create.md](sandbox-create.md) · [secret.md](secret.md) (modern-domain only) · [snapshot.md](snapshot.md) (modern-domain only)

## Connection parameter table

| Parameter | Type | Required / default | Purpose |
|---|---|---|---|
| `PPIO_DOMAIN` / `--domain` | string | `sandbox.ppio.cn` | Select region/API domain. Set to `cn-beijing-1.sandbox.ppio.com` for v2. |
| `PPIO_API_URL` / `--api-url` | URL | derived from domain | Override API endpoint for custom/local deployments. |
| `PPIO_API_KEY` / `--api-key` | string | required unless access token/config | API authentication credential. |
