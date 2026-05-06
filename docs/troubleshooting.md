# Troubleshooting

## Directory or prompt.md not found

```
✗ /path/to/my-service does not exist
```

The directory you passed to `create` or `run` doesn't exist. Create it first:

```bash
mkdir my-service
```

```
✗ /path/to/my-service/prompt.md not found
```

The directory exists but doesn't contain a `prompt.md` file. Create one:

```bash
echo "Build me a todo service" > my-service/prompt.md
```

## Not logged in

```
✗ Not logged in. Run 'npx @skiplabs/skipper login' first.
```

You need to authenticate before using `create` or `run`:

```bash
npx @skiplabs/skipper login
```

## Docker not running

If Docker isn't running, `create` and `run` will fail when trying to pull the image or start a container. Make sure Docker Desktop (or the Docker daemon) is running:

```bash
docker info
```

## Container already exists

```
✗ skipper-my-service-c6235218 already exists. Run 'npx @skiplabs/skipper stop my-service' first.
```

A container from a previous `run` is still active. Stop it first:

```bash
npx @skiplabs/skipper stop my-service
```

## Port in use

```
✗ Port 4000 is already in use. Use --port <other> to pick a different host port.
```

The port you specified with `--port` is already taken. Pick a different one:

```bash
npx @skiplabs/skipper run my-service --port 4001
```

If you're using the default port (3000) without `--port`, Skipper automatically tries ports 3001-3009. If all are taken:

```
✗ Ports 3000-3009 are all in use. Use --port <other>.
```

Free up a port or specify one explicitly with `--port`.

## Generation failed

```
✗ Generation failed (exit 1)
  Re-run with SKIPPER_DEBUG=1 for more detail.
```

The code generator encountered an error. Re-run with debug output enabled:

```bash
SKIPPER_DEBUG=1 npx @skiplabs/skipper create my-service
```

Specific exit codes:

| Exit code | Meaning                                              |
| --------- | ---------------------------------------------------- |
| 125       | Docker daemon refused the run -- check `docker info` |
| 126       | Container entrypoint not executable                  |
| 127       | Container entrypoint not found                       |

## Container exited with an error

```
✗ Container exited with code 1
```

The service crashed after starting. Check the logs for details:

```bash
npx @skiplabs/skipper logs my-service
```

Common causes:

- **Missing environment variables** -- the generated service may require environment variables (like `JWKS_URL`) to be configured. Check the generated `README.md` in your project directory for required configuration.
- **Port conflicts inside the container** -- unlikely but possible if the generated code has issues.

## Updating the Docker image

If you suspect the Docker image is outdated, force a re-pull:

```bash
npx @skiplabs/skipper create my-service --update
npx @skiplabs/skipper run my-service --update
```

## Checking overall state

To get a full picture of your Skipper setup (Docker status, login, balance, projects):

```bash
npx @skiplabs/skipper status
```

For machine-readable output:

```bash
npx @skiplabs/skipper status --json
```
