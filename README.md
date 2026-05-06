# Skipper

Skipper is a CLI tool from [SkipLabs](https://www.skiplabs.io) that generates fully functional backend services from a natural language prompt. You describe the service you want, and Skipper generates the code, tests, and an OpenAPI specification -- ready to run locally with a single command.

## Prerequisites

- **Node.js** >= 18
- **Docker** must be installed and running

## Quick start

```bash
# 1. Log in (one-time)
npx @skiplabs/skipper login

# 2. Create a project directory with a prompt
mkdir my-service
echo "Build me a todo service" > my-service/prompt.md

# 3. Generate the service
npx @skiplabs/skipper create my-service

# 4. Run it
npx @skiplabs/skipper run my-service
```

Your service will be available at `http://localhost:3000`.

## What gets generated

After running `create`, your project directory will contain:

- TypeScript source code implementing your service
- `openapi.json` -- the API specification
- `package.json`, `tsconfig.json` -- project configuration
- Unit tests and test data

## Building a UI

While your service is running, you can hand the generated OpenAPI spec to your usual coding agent to scaffold a matching frontend:

```
read my-service/openapi.json and build the related web app
for it. Service is running on http://localhost:3000
```

## Documentation

- [Getting Started](docs/getting-started.md) -- step-by-step walkthrough
- [CLI Reference](docs/cli-reference.md) -- all commands, options, and flags
- [Configuration](docs/configuration.md) -- config files, environment variables, Docker
- [Troubleshooting](docs/troubleshooting.md) -- common errors and how to fix them
