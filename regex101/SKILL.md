---
name: regex101
description:
  Create, save, explain, and refine regular expressions with the regex101 MCP server. Use when a
  user wants to build regex or change a regex101 workspace, add representative unit tests, keep editing
  the same saved regex, or request the Pro explanation for a regex.
---

# regex101

Use the connected regex101 MCP server for regex work. If its tools are unavailable, read
[MCP setup](references/setup.md), guide the client setup in the same conversation, and then resume
the original task. Use the client's native setup action yourself when the environment permits. Ask
the user only to complete browser sign-in and consent. Never ask for or handle a bearer token.

## Show API data verbatim

When the user requests data that a regex101 tool returns, show the requested value from the latest
tool result verbatim. Do not replace it with an interpretation or a value recreated from memory.

- For a string field, copy the exact field value.
- For an object or array, copy its exact JSON text from the tool response.
- Put each value in an unlabeled Markdown fenced code block.
- Use at least three backticks for the fence.
- If the value contains backticks, use a fence longer than its longest backtick sequence.
- Do not escape the content inside the fence.
- Preserve all characters, whitespace, indentation, line breaks, key order, and escaping.
- Do not interpret, correct, normalize, trim, truncate, summarize, translate, or reformat a value.
- Do not add an interpretation before or after the value unless the user also asks for one.
- If you show multiple values, add only a short field-name label before each code block.
- A raw URL is the only exception. Return the exact raw URL outside a code block so it stays
  clickable.

This rule applies to patterns, flags, delimiters, test strings, substitutions, unit tests, titles,
tags, explanations, and every other API field.

## Tools

- `get_workspace`: Get a saved workspace. Pass `permalinkFragment`. Omit `version` to get the
  latest version.
- `get_user_tags`: Get the tags used by the signed-in user.
- `find_workspaces_by_tags`: Find saved workspaces that have every supplied tag. Use `page` to
  continue through the results.
- `save_workspace`: Create a workspace with a regex, sample strings, flavor settings, and useful
  unit tests.
- `update_workspace`: Add a version to an existing workspace. Pass the existing
  `permalinkFragment` and the complete updated workspace.
- `delete_workspace`: Permanently delete a workspace owned by the signed-in user.
- `delete_workspace_version`: Permanently delete one owned version. Deleting the only version also
  deletes the workspace.
- `set_workspace_title`: Set a specific title after a create or edit.
- `set_workspace_tags`: Replace the workspace tags after a create or edit.
- `explain_regex`: Ask the regex101 Pro API to explain a pattern.

## Create a workspace

1. Confirm the goal, flavor, flags, and representative sample text.
2. Use PCRE2 if the user does not name a language or flavor.
3. Add focused unit tests when they make the regex more robust. Include positive, negative,
   boundary, and regression cases when the requirements support them.
4. Do not invent business rules. Ask when a test depends on an unclear rule.
5. Call `save_workspace`.
6. Set a clear title and concise tags with the metadata tools.
7. Keep the returned `permalinkFragment` and `version` in the conversation state.
8. Return the exact workspace URL from the tool response as a raw URL.

Use lowercase tags for the domain, format, and important regex feature. Do not use a generic title
such as "Regex".

## Edit the current workspace

Treat a follow-up about the same regex as an edit.

1. Reuse the current `permalinkFragment`.
2. Call `get_workspace` when you need fields that are not in the conversation.
3. Preserve fields that the user did not ask to change.
4. Update affected unit tests. Add a regression case when useful.
5. Call `update_workspace`. Never call `save_workspace` for the current workspace.
6. Update the title and tags when the new behavior changes their meaning.
7. Keep the new version in the conversation state.

If the user identifies a previous regex by subject instead of URL, call `get_user_tags`, then use
`find_workspaces_by_tags` to locate it. Continue with `get_workspace` and `update_workspace`.

## Delete a workspace

Use a delete tool only when the user explicitly asks to delete a workspace or version. Before you
call `delete_workspace_version`, tell the user that deleting the only version also deletes the
workspace.

## Explain a regex

Call `explain_regex` with `format: "text"`. Show its `explanation` field. Apply the verbatim API data
rule.

If the tool reports that Pro is required, give the user the
[regex101 account page](https://regex101.com/account/all). Do not retry until the user confirms that
access changed.
