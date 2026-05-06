# Configuration

## Config file

Skipper stores its configuration in `~/.skipper/config.yml`. This file is created automatically when you first log in.

**File permissions:** The `~/.skipper` directory is created with mode `0700` and the config file with mode `0600` (owner-only access) to protect your API token.

### Structure

```yaml
auth:
  apiKey: <your-api-token>
projects:
  /absolute/path/to/project:
    name: my-service
    createdAt: "2026-01-15T10:30:00.000Z"
    lastImage: skiplabs/skipper:latest
    lastRunAt: "2026-01-16T14:00:00.000Z"
    lastRunImage: skiplabs/skipper:latest
    lastPort: 3000
```

### Fields

**auth:**

| Field    | Description            |
| -------- | ---------------------- |
| `apiKey` | Your Skipper API token |

**projects (per project):**

| Field          | Description                                  | Set by    |
| -------------- | -------------------------------------------- | --------- |
| `name`         | Display name (defaults to directory basename) | `create`  |
| `createdAt`    | ISO timestamp of first generation             | `create`  |
| `lastImage`    | Docker image used for generation              | `create`  |
| `lastRunAt`    | ISO timestamp of last run                     | `run`     |
| `lastRunImage` | Docker image used for last run                | `run`     |
| `lastPort`     | Port used in last run                         | `run`     |

You generally don't need to edit this file manually. The CLI manages it for you.

## Environment variables

| Variable          | Description                                                     | Default |
| ----------------- | --------------------------------------------------------------- | ------- |
| `SKIPPER_ENV`     | Set to `staging` to use the staging backend and image           | `prod`  |
| `SKIPPER_DEBUG`   | Set to `1` for detailed error output when generation fails      |         |

### `SKIPPER_ENV`

Controls which backend environment Skipper connects to. When set to `staging`:

- Login URL: `https://www.skipper-staging.skiplabs.io/`
- API URL: `https://api.skipper-staging.skiplabs.io`
- Docker image: `skiplabs/skipper:staging` (instead of `:latest`)
- A warning is displayed on CLI startup

Most users should leave this unset (defaults to `prod`).

### `SKIPPER_DEBUG`

When generation fails, re-run with this variable set for more detail:

```bash
SKIPPER_DEBUG=1 npx @skiplabs/skipper create my-service
```

## Docker

Skipper uses Docker to run both the code generator (during `create`) and the generated service (during `run`). Docker must be installed and running.

### Image

The Docker image `skiplabs/skipper:latest` is pulled automatically on first use. To force an update:

```bash
npx @skiplabs/skipper create my-service --update
npx @skiplabs/skipper run my-service --update
```

### Container naming

Running containers are named `skipper-<slug>-<hash>`, where:

- `<slug>` is the project directory name (lowercased, non-alphanumeric characters replaced with `-`, truncated to 32 characters)
- `<hash>` is the first 8 characters of a SHA-256 hash of the absolute path

This naming is deterministic -- the same project directory always produces the same container name.

### Ports

The service runs on port 8080 inside the container. The `run` command maps this to a host port (default 3000). The container is run with `--rm` so it is automatically cleaned up when stopped.
