# regex101 skill

This repository contains a portable [Agent Skill](https://agentskills.io) that teaches coding agents
to use regex101 for regex work. Claude Code, Codex, Cursor, and other compatible agents all read
`regex101/SKILL.md`.

`regex101/agents/openai.yaml` is optional Codex UI and dependency metadata. It is not the skill
entry point, and Claude Code and Cursor do not need vendor-specific YAML files.

## Install

Install the skill with the [`skills` CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add firasdib/regex101-skills --skill regex101 --global
```

The CLI prompts you to select an agent. You can also select Codex or Claude Code directly:

```sh
npx skills add firasdib/regex101-skills --skill regex101 --global --agent codex
npx skills add firasdib/regex101-skills --skill regex101 --global --agent claude-code
npx skills add firasdib/regex101-skills --skill regex101 --global --agent cursor
```

Install it for all three at once:

```sh
npx skills add firasdib/regex101-skills --skill regex101 --global \
  --agent codex --agent claude-code --agent cursor
```

Omit `--global` for a project installation. The `skills` CLI also supports other agents that
implement Agent Skills.

## Local development

Clone the repository, then install the local checkout:

```sh
git clone https://github.com/firasdib/regex101-skills.git
cd regex101-skills
npx skills add . --skill regex101 --global \
  --agent codex --agent claude-code --agent cursor
```

Choose the symlink installation when prompted so all selected agents share one installed copy.
Rerun the local install command after changing this checkout, then restart the agent to reload the
skill metadata and instructions.

Check that the local skill is discoverable with:

```sh
npx skills add . --list
```

## Connect the MCP server

The skill guides the user through this when its tools are unavailable. The user should only need to
complete the browser sign-in and consent step; the agent should add the server with the client's
native command or installer.

### Codex

```sh
codex mcp add regex101 --url https://regex101.com/mcp
codex mcp login regex101
```

### Claude Code

```sh
claude mcp add --transport http --scope user regex101 https://regex101.com/mcp
```

Then open `/mcp` in Claude Code and authenticate `regex101`.

### Cursor

[Add regex101 to Cursor](https://cursor.com/install-mcp?name=regex101&config=eyJ1cmwiOiJodHRwczovL3JlZ2V4MTAxLmNvbS9tY3AifQ%3D%3D),
approve the server, and complete OAuth when Cursor prompts. Cursor CLI users can start authentication
after installation with:

```sh
agent mcp login regex101
```

### Other clients

Add this URL as a remote Streamable HTTP server named `regex101`:

```text
https://regex101.com/mcp
```

The client must support OAuth for remote MCP servers. Do not create or paste a bearer token; the
client discovers the authorization server and stores the credential outside the model context. See
the [regex101 MCP documentation](https://docs.regex101.com/application/mcp) for more information.

## How authentication works

1. The client contacts `/mcp` without a token. regex101 replies with `401 Unauthorized` and points
   it to the OAuth protected-resource metadata.
2. The client discovers the authorization and token endpoints. It dynamically registers its name
   and loopback or HTTPS callback URL; no client secret is required.
3. The client creates a PKCE verifier and challenge, then opens the regex101 authorization page in
   the user's browser.
4. The user signs in to regex101 if necessary and approves the named client.
5. regex101 redirects a short-lived, one-time authorization code to the client. The client exchanges
   the code and PKCE verifier for an access token.
6. The client stores that token and sends it in the `Authorization` header for MCP requests. The
   token is the user's signed regex101 session credential, so normal ownership and Pro access rules
   still apply. Signing out invalidates the shared session.

The model does not need—and should never receive—the access token. Its only role is to initiate the
client's setup flow and tell the user what browser action remains.
