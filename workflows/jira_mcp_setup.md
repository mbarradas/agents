---
description: Integrate Jira MCP server to track AI Agent progress across workspaces.
---

# Tracking AI Projects in Jira with MCP

When creating new workspaces (like a new Altair AI Studio Python Extension), integrating the Model Context Protocol (MCP) server for Jira allows an AI Agent to actively track its implementation tasks, Epics, and progress automatically.

## 1. Prerequisites
- **Node.js**: Ensure Node.js (`npx`) is installed over the network/proxy and available in the system path (`npx -v`).
- **Jira Credentials**:
  - `ATLASSIAN_SITE_NAME`: The subdomain prefix of your Jira URL (e.g., if `alterweb.atlassian.net`, use `alterweb`).
  - `ATLASSIAN_USER_EMAIL`: The Atlassian login email.
  - `ATLASSIAN_API_TOKEN`: The user API token generated from `id.atlassian.com`.

## 2. Configuring the MCP Server

The community has transitioned across several Jira MCP implementations. As of 2026, the primary working package via npm is `@aashari/mcp-server-atlassian-jira`.

1. Locate the AI Agent MCP configuration file (typically `~/.gemini/antigravity/mcp_config.json`).
2. Add the Jira server block exactly like this:

```json
{
  "mcpServers": {
    "jira": {
      "command": "npx",
      "args": [
        "-y",
        "@aashari/mcp-server-atlassian-jira"
      ],
      "env": {
        "ATLASSIAN_SITE_NAME": "<site-prefix>",
        "ATLASSIAN_USER_EMAIL": "<your-email>",
        "ATLASSIAN_API_TOKEN": "<your-api-token>"
      }
    }
  }
}
```

3. Restart the ID/Agent or use the Reload function so the `.json` configuration is parsed and `npx` launches the server in the background.

## 3. Creating Project Taxonomies

Once the MCP Tooling `[mcp_jira]` is active, standard protocol involves:
1. **Fetch Projects**: Use `jira_get` on `/rest/api/3/project/search` to find your target project Key (e.g., `SCRUM`).
2. **Fetch Schemas**: Use `jira_get` on `/rest/api/3/issuetype` to verify the numeric IDs for Epics vs Tasks as Jira dynamically assigns them based on custom workspace schemas.
3. **Build the Epic**: Create the overarching tracking Epic (`jira_post` to `/rest/api/3/issue` with `issuetype` equal to the Epic ID).
4. **Build Tasks**: Append sub-tasks linked to the `parent.key` of the newly created Epic.

By enforcing this structure, any new agent session can immediately establish a tracking timeline without getting lost!
