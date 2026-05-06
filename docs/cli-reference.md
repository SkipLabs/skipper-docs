# CLI Reference

## Installation

Skipper is available as an npm package. You can run it directly with `npx`:

```bash
npx @skiplabs/skipper [command]
```

Or install it globally:

```bash
npm install -g @skiplabs/skipper
skipper [command]
```

## Global options

| Option            | Description               |
| ----------------- | ------------------------- |
| `-v`, `--version` | Print the current version |
| `-h`, `--help`    | Show help                 |

---

## `login`

Authenticate with Skipper.

```bash
npx @skiplabs/skipper login
```

Opens your browser to obtain an API token. Paste the token into the terminal when prompted. The token is saved to `~/.skipper/config.yml`.

If the browser doesn't open automatically, the URL is printed so you can open it manually.

### `login status`

Check whether you are currently logged in.

```bash
npx @skiplabs/skipper login status
```

Output:

```
✓ Logged in (token ••••G8tw from ~/.skipper/config.yml)
```

---

## `logout`

Clear your saved API token.

```bash
npx @skiplabs/skipper logout
```

Output:

```
✓ Logged out (cleared ~/.skipper/config.yml).
```

---

## `create`

Generate a service from a `prompt.md` file.

```bash
npx @skiplabs/skipper create <path> [options]
```

**Aliases:** `iterate`

### Arguments

| Argument | Description                                            |
| -------- | ------------------------------------------------------ |
| `path`   | Directory containing a `prompt.md` file (must exist)   |

### Options

| Option     | Description                                  |
| ---------- | -------------------------------------------- |
| `--update` | Force re-pull of the Docker image before running |

### Requirements

- The directory at `<path>` must exist
- `<path>/prompt.md` must exist and be a regular file
- You must be logged in
- Docker must be running

### What it does

1. Validates the directory and `prompt.md` file
2. Pulls the Docker image if needed (or if `--update` is passed)
3. Runs the code generator inside a Docker container
4. Writes generated files into `<path>`
5. Records the project in `~/.skipper/config.yml`

### Examples

```bash
# Basic usage
mkdir my-service
echo "Build me a todo service" > my-service/prompt.md
npx @skiplabs/skipper create my-service

# Force image update
npx @skiplabs/skipper create my-service --update

# Iterate on an existing service (same command)
npx @skiplabs/skipper iterate my-service
```

---

## `run`

Start a generated service in a Docker container.

```bash
npx @skiplabs/skipper run <path> [options]
```

### Arguments

| Argument | Description                  |
| -------- | ---------------------------- |
| `path`   | Project directory (required) |

### Options

| Option              | Description                                      | Default |
| ------------------- | ------------------------------------------------ | ------- |
| `-p`, `--port <n>`  | Host port to bind                                | `3000`  |
| `--update`          | Force re-pull of the Docker image before running |         |

### Port selection

- If the default port (3000) is in use and no explicit `--port` was given, Skipper automatically scans ports 3001-3009 and uses the first free one.
- If you specify `--port` explicitly and that port is in use, the command fails with an error.

### What it does

1. Validates the directory exists and you are logged in
2. Checks that no container is already running for this project
3. Picks a free port
4. Runs the service inside a Docker container (installs dependencies, transpiles TypeScript, starts the server)
5. Displays the service URL
6. Runs in the foreground -- press `CTRL+C` to stop

### Examples

```bash
# Default port 3000
npx @skiplabs/skipper run my-service

# Custom port
npx @skiplabs/skipper run my-service --port 4000

# Re-pull image first
npx @skiplabs/skipper run my-service --update
```

---

## `stop`

Stop a running service.

```bash
npx @skiplabs/skipper stop <path>
```

### Arguments

| Argument | Description                               |
| -------- | ----------------------------------------- |
| `path`   | Project directory path or tracked project name |

### What it does

1. Resolves the path (can be a directory path or a project name from your tracked projects)
2. Stops and removes the Docker container
3. If the container isn't running, prints a warning and exits cleanly

### Examples

```bash
# By path
npx @skiplabs/skipper stop my-service

# By relative path
npx @skiplabs/skipper stop ./scratch/demo
```

---

## `logs`

View logs from a running service's container.

```bash
npx @skiplabs/skipper logs <path> [options]
```

### Arguments

| Argument | Description                  |
| -------- | ---------------------------- |
| `path`   | Project directory (required) |

### Options

| Option             | Description                       | Default |
| ------------------ | --------------------------------- | ------- |
| `-f`, `--follow`   | Stream log output in real time    |         |
| `-n`, `--tail <n>` | Number of trailing lines to show  | `all`   |

### Examples

```bash
# Show all logs
npx @skiplabs/skipper logs my-service

# Follow live output
npx @skiplabs/skipper logs my-service -f

# Last 100 lines, then follow
npx @skiplabs/skipper logs my-service -n 100 -f
```

---

## `projects` / `ls`

List all Skipper projects created on this machine.

```bash
npx @skiplabs/skipper projects [options]
npx @skiplabs/skipper ls [options]
```

### Options

| Option   | Description                       |
| -------- | --------------------------------- |
| `--json` | Output raw JSON project data      |

### Output

Displays a table with columns:

| Column   | Description                            |
| -------- | -------------------------------------- |
| NAME     | Project name (directory basename)      |
| STATUS   | `running` or `stopped`                 |
| CREATED  | When the project was first generated   |
| LAST RUN | When the project was last started      |
| PATH     | Directory path                         |

### Example

```
NAME         STATUS   CREATED      LAST RUN     PATH
my-service   running  2 days ago   5 mins ago   ~/projects/my-service
demo         stopped  1 month ago  3 days ago   ~/scratch/demo
```

---

## `balance`

Show your remaining Skipper credit balance.

```bash
npx @skiplabs/skipper balance
```

### Output

```
Total        $94.67
  Regular      $71.22
  Promotional  $23.45

  Promotions:
    LAUNCH       $20.00
    REFERRAL     $3.45
```

Shows a breakdown of regular and promotional credits, including individual promotions if any are active.

---

## `status`

Show a digest of the current Skipper state.

```bash
npx @skiplabs/skipper status [options]
```

### Options

| Option   | Description                       |
| -------- | --------------------------------- |
| `--json` | Output machine-readable JSON      |

### Output

```
  CLI version  0.4.0
  Environment  prod
  Docker       running
  Image        skiplabs/skipper:latest (cached)
  Logged in    yes ••••G8tw
  Balance      $94.67
  Config       ~/.skipper/config.yml

  Projects (2 total, 1 running)

  NAME         STATUS   LAST RUN     PORT  PATH
  my-service   running  5 mins ago   3000  ~/projects/my-service
  demo         stopped  3 days ago   3001  ~/scratch/demo
```

Shows: CLI version, Docker status, whether the image is cached locally, login state, balance, and a summary of all projects.
