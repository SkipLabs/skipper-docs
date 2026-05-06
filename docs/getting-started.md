# Getting Started

This guide walks through creating and running your first Skipper service.

## 1. Install prerequisites

Skipper requires:

- **Node.js** >= 18 (or Bun >= 1.3.13)
- **Docker** -- must be installed and running

Verify Docker is running:

```bash
docker info
```

## 2. Log in

Run the login command:

```bash
npx @skiplabs/skipper login
```

This opens your browser to `https://www.skipper.skiplabs.io/` where you can obtain an API token. Paste the token back into the terminal when prompted.

```
Attempting to open your default browser…
If the browser does not open, open the following url: https://www.skipper.skiplabs.io/

Enter token: eyJh•••G8tw

✓ Saved token ••••G8tw

Welcome to Skipper!

Balance  $100.00
```

You only need to log in once -- the token is saved to `~/.skipper/config.yml`.

To check your login status later:

```bash
npx @skiplabs/skipper login status
```

## 3. Write a prompt

Create a directory for your service and add a `prompt.md` file describing what you want:

```bash
mkdir my-service
echo "Build me a todo service" > my-service/prompt.md
```

The `prompt.md` file is a plain Markdown file. It should describe the service you want Skipper to build in natural language. You can be as simple or detailed as you like:

**Simple:**

```markdown
Build me a todo service
```

**Detailed:**

```markdown
Build a task management service with:

- Tasks have a title, description, status (pending/in-progress/done), and priority (low/medium/high)
- Users can create, update, delete, and list tasks
- Tasks can be filtered by status and priority
- Support pagination on list endpoints
```

## 4. Generate the service

Run `create` pointing at your project directory:

```bash
npx @skiplabs/skipper create my-service
```

Skipper pulls the Docker image (first run only), then generates your service. You'll see progress output as it works through each stage:

```
✅ Unit test environment [████████████████████] 1/1 100%
✅ OpenAPI specification [████████████████████] 2/2 100%
✅ External access check [████████████████████] 1/1 100%
✅ Skip Runtime specification [████████████████████] 4/4 100%
✅ Code generation [████████████████████] 99/99 100%
✅ Test data sets generation [████████████████████] 15/15 100%
✅ Unit tests of mappers, reducers and computes [████████████████████] 62/62 100%
✅ Unit tests of resources and service [████████████████████] 28/28 100%
✅ Generation completed in 136.7s for a cost of $5.33.
Your credit balance after generation is $94.67
✓ Done
```

The generation cost is deducted from your account balance. You can check your balance at any time with `npx @skiplabs/skipper balance`.

After generation, your project directory will contain:

- TypeScript source code
- `openapi.json` -- the API specification
- `package.json` and `tsconfig.json`
- Unit tests and test data

## 5. Run the service

Start the service:

```bash
npx @skiplabs/skipper run my-service
```

```
✓ Started skipper-my-service-c6235218

  ╭──────────────────────────────╮
  │                              │
  │   my-service                 │
  │   →  http://localhost:3000   │
  │                              │
  ╰──────────────────────────────╯

  Press CTRL+C to stop.
```

Your service is now running at `http://localhost:3000`. You can test it by hitting the endpoints described in `openapi.json`.

To use a different port:

```bash
npx @skiplabs/skipper run my-service --port 4000
```

If port 3000 is already in use and you didn't specify a port explicitly, Skipper will automatically try ports 3001-3009.

Press `CTRL+C` to stop the service.

## 6. Iterate on the service

To change or extend your service, edit the `prompt.md` file and re-run `create`:

```bash
# Edit the prompt
vim my-service/prompt.md

# Regenerate
npx @skiplabs/skipper create my-service
```

The `iterate` command is an alias for `create` -- they do the same thing:

```bash
npx @skiplabs/skipper iterate my-service
```

## 7. Build a frontend

While the service is running, you can use any coding agent (Claude, Cursor, Copilot, etc.) to generate a matching UI. Pass it the OpenAPI spec:

```
read my-service/openapi.json and build the related web app
for it. Service is running on http://localhost:3000
```

## Next steps

- [CLI Reference](cli-reference.md) -- all commands and options
- [Configuration](configuration.md) -- config files and environment variables
- [Troubleshooting](troubleshooting.md) -- common errors and fixes
