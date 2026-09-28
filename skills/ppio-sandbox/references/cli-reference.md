# CLI reference

The `ppio` CLI covers auth, sandbox, template, snapshot, secret, and volume operations. Every command reference keeps its positional arguments, flags, defaults, units, output options, and mutual exclusions beside its examples. Install with `npm i -g ppio-sandbox-cli`. Commands that register `--output <format>` (`-o`) accept `pretty` (default), `json`, or `yaml`. The old `--format` and `--json` are deprecated aliases for `--output`. Sandbox and template commands are shown inline in the module references (create.md, kill.md, list.md, template-*.md, …); this file documents the **auth** command group and gives a command index.

## CLI parameter conventions

Every command also accepts `-h, --help`. Only the three connection options below are **global** (accepted before the subcommand and inherited by subcommands), together with `-V, --version`:

| Global option | Type / default | Purpose |
|---|---|---|
| `--domain <domain>` | string; `PPIO_DOMAIN` or `sandbox.ppio.cn` | Region / sandbox domain. |
| `--api-url <api-url>` | URL; derived from domain | API endpoint override. |
| `--request-timeout <duration>` | duration; SDK default | API request deadline, e.g. `120s` or `5m`. |

Everything else is **per-command** — passing one of these at the top level (`ppio --api-key … sandbox list`) is an unknown-option error. The API key comes from the `PPIO_API_KEY` environment variable or the stored login; there is no global `--api-key` flag:

| Option | Where it is accepted | Purpose |
|---|---|---|
| `--api-key <key>` | `auth login` only | Log in with a literal API key. |
| `-t, --team <team-id>` | `template list`, `template delete`, `template publish`, `template unpublish` (and deprecated `template build`) | Team associated with the operation. |
| `-p, --path <path>` | `template create`, `template init`, `template delete`, `template publish`, `template unpublish`, `template migrate`, `volume mount` (and deprecated `template build`) | Root directory for the command. |
| `--config <ppio-toml>` | `template delete`, `template publish`, `template unpublish`, `template migrate`, `sandbox create` (and deprecated `template build`) | Config file (`./ppio.toml`) for the older template layout. |
| `-s, --select` | `template delete`, `template publish`, `template unpublish` | Interactive template picker. |

### Output flags

Commands that produce structured output register `-o, --output <format>` (`pretty` default, plus `json` and `yaml`), with `-f, --format` and `--json` as deprecated aliases: `template list/get/exists/build-status`, `sandbox list/info/create/events/quota`, `snapshot list/create`, `secret list/get`, `volume list/get/create`.

`sandbox logs` and `sandbox metrics` are the exception — they have **no** `--output`/`--json`, and their `-f` means `--follow`, not `--format`. Passing an option absent from a command's own table is an unknown-option error.

## Authentication (`auth`)

Credentials are stored locally at `~/.ppio/config.json`.

```bash
ppio auth login        # browser-based sign-in; captures token, selects default team
ppio auth logout       # sign out (deletes ~/.ppio/config.json)
ppio auth info         # show current user (email) and selected team
ppio auth configure    # switch the active team for the current session
```

- `login` opens a browser authorization page; if already logged in it reports the current session. To sign in as a different user, `logout` first.
- `configure` requires being logged in; it lists your teams and saves the chosen team's name/ID/API key.

### `ppio auth login`

| Parameter | Required / default | Purpose |
|---|---|---|
| `--api-key <key>` | exactly one key source; optional | Login with a literal API key. |
| `--api-key-env <name>` | mutually exclusive | Read the API key from the named environment variable. |
| `--api-key-stdin` | mutually exclusive | Read a trimmed API key from stdin. |
| `--email <email>` | optional | Email to store for headless login. |
| `--force` | false | Replace the existing local login configuration. |

With no key source, the CLI uses an existing config, headless `PPIO_API_KEY`, or browser login.

| Command | Parameters | Purpose |
|---|---|---|
| `ppio auth logout` | none | Delete the local CLI config and sign out. |
| `ppio auth info` | none | Show the current user and selected team. |
| `ppio auth configure` | none; interactive prompt | Select and save the active team. |

## Additional sandbox/template commands

The command examples and parameter tables live with their resource references: [network](sandbox-network.md), [hotplug memory](sandbox-timeout.md), and [template management](template-list-delete.md).

## Command index

| Area | Command | Reference |
|------|---------|-----------|
| Auth | `auth login/logout/info/configure` | above |
| Region | `PPIO_DOMAIN` (default `cn-shanghai-1`, v1; set to the v2 domain for Secrets/Snapshots) | [region.md](region.md) |
| Sandbox — create | `sandbox create [template]` (alias `cr`) | [create.md](sandbox-create.md) |
| Sandbox — list | `sandbox list` (alias `ls`) | [list.md](sandbox-list.md) |
| Sandbox — connect (remote shell) | `sandbox connect <id>` (alias `cn`) | [connect.md](sandbox-connect.md) |
| Sandbox — exec | `sandbox exec <id> -- <cmd>` (alias `ex`) | [run-command.md](sandbox-run-command.md) |
| Sandbox — pause/resume | `sandbox pause/resume <id>` | [pause-resume.md](sandbox-pause-resume.md) |
| Sandbox — kill | `sandbox kill <id>` / `-a` | [kill.md](sandbox-kill.md) |
| Sandbox — metrics/events | `sandbox metrics/events <id>` | [info-metrics-events.md](sandbox-info-metrics-events.md) |
| Sandbox — network egress | `sandbox network <id> --allow-out/--deny-out` | [sandbox-network.md](sandbox-network.md) |
| Sandbox — controller authentication | `sandbox create <template> --secure` / `--no-secure` | [sandbox-secured-access.md](sandbox-secured-access.md) |
| Sandbox — set timeout | `sandbox set-timeout <id> <timeout>` (e.g. `5m`, `1h`) | [sandbox-timeout.md](sandbox-timeout.md) |
| Sandbox — hotplug memory | `sandbox hotplug-memory <id> <size-mib>` (alias `hp`) — **deprecated, pending removal; hidden from `--help`** | [sandbox-timeout.md](sandbox-timeout.md) |
| Snapshot | `snapshot create/list/delete` (aliases `ls`, `rm`) | [snapshot.md](snapshot.md) |
| Secret | `secret create/get/list/update/delete` (aliases `info`, `ls`, `rm`) | [secret.md](secret.md) |
| Volume | `volume create/list/get/delete/mount/unmount` (aliases `ls`, `info`, `rm`) | [volume.md](volume.md) |
| Volume — mount at sandbox creation | `sandbox create <template> --volume-mount /mnt/data=my-data` (repeatable) | [volume.md](volume.md#mount-at-sandbox-creation) |
| Template — build | `template create [name]` (alias `ct`) | [template-build.md](template-build.md) |
| Template — build (deprecated) | `template build` (alias `bd`) — superseded by `template create`; pending removal | [template-build.md](template-build.md) |
| Template — list/delete | `template list` / `template delete` (aliases `ls`, `dl`) | [template-list-delete.md](template-list-delete.md) |
| Template — publish/unpublish | `template publish/unpublish [template]` (aliases `pb`, `upb`) | [template-list-delete.md](template-list-delete.md) |
| Template — migrate | `template migrate` — converts `ppio.Dockerfile` + `ppio.toml` to the Template SDK format | [template-list-delete.md](template-list-delete.md) |
| Template — tags | `template tags get/add/remove <templateID> [tags]` | [template-tags.md](template-tags.md) |

### Commands not covered by a reference file

These exist in CLI 2.1.0 but have no dedicated reference here. Run `ppio <group> <cmd> --help` for their flags:

| Command | Purpose |
|---|---|
| `sandbox logs <id>` (alias `lg`) | Show sandbox logs. `-f` follows; no `--output`. |
| `sandbox cp <source> <destination>` | Copy a file between the local filesystem and a sandbox, using `<sandbox-id>:<path>` for the remote side. `-u, --user` sets the sandbox user. |
| `sandbox clone <id>` | Clone a sandbox. |
| `sandbox reset <id>` | Reset a sandbox. |
| `sandbox commit <id>` | Commit a sandbox to create a snapshot template. |
| `sandbox quota` | Show sandbox quota. |
| `template init` (alias `it`) | Initialize a new template using the SDK. |
| `template exists <templateName>` | Check whether a template exists. |
| `template get <templateID>` (alias `info`) | Show template information. |
| `template alias-exists <alias>` | Check whether a template alias exists. |
| `template build-status <templateID> <buildID>` | Get template build status. |
