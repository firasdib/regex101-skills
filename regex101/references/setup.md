# MCP setup

Read this only when the regex101 MCP tools are unavailable.

Configure the server as part of the current task. Prefer a client-native command, installer, or API
over asking the user to edit configuration. Preserve existing MCP configuration. After adding the
server, start the client's OAuth action and ask the user only to finish browser sign-in and approve
the named client. Then verify the tools are available and resume the original regex task.

Never ask the user for a bearer token, put a token in configuration, or expose one to the model.

## Codex

Check for an existing `regex101` server with `codex mcp list`. Codex may have already resolved the
dependency declared in `agents/openai.yaml`. If it is absent, run:

```sh
codex mcp add regex101 --url https://regex101.com/mcp
```

Start authentication with:

```sh
codex mcp login regex101
```

Ask the user to complete the browser flow, then check `codex mcp list`.

## Claude Code

Check `claude mcp list`. If the server is absent, run:

```sh
claude mcp add --transport http --scope user regex101 https://regex101.com/mcp
```

Tell the user to open `/mcp`, select `regex101`, and authenticate. Continue after they complete the
browser flow.

## Cursor

Open the one-click installer for the user when possible:

[Add regex101 to Cursor](https://cursor.com/install-mcp?name=regex101&config=eyJ1cmwiOiJodHRwczovL3JlZ2V4MTAxLmNvbS9tY3AifQ%3D%3D)

After the user approves installation, Cursor should prompt for OAuth. Cursor CLI users can also run:

```sh
agent mcp login regex101
```

## Other clients

Use the client's native MCP registration with:

- Name: `regex101`
- Transport: Streamable HTTP
- URL: `https://regex101.com/mcp`
- Authentication: OAuth discovered from the server

If the client cannot add remote Streamable HTTP servers or complete MCP OAuth, explain that it is
not compatible. Do not fall back to asking the user for a token.

## What the user sees

The client discovers regex101's OAuth endpoints, registers itself dynamically, and opens a browser.
The user signs in to regex101 if needed, reviews the client name, and approves or denies access. The
client receives and stores the credential outside the model context.
