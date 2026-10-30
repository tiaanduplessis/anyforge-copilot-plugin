# AnyForge for GitHub Copilot

A GitHub Copilot plugin that connects Copilot to [AnyForge](https://anyforge.ai).
It adds:

- **The AnyForge MCP server** (`https://crew.anyforge.ai/api/mcp`): read and
  update work items, initiatives, crews and approvals from Copilot. You sign in
  with your AnyForge account in the browser; there is no key to paste.
- **The `anyforge-work-items` skill**: how to deliver an AnyForge work item
  from Copilot, including naming its code in the branch and pull request so
  AnyForge links the pull request and moves the item.

Everything runs as you, within your role in the AnyForge workspace and the
scopes you approve at sign-in.

## Install

### Copilot CLI

```bash
copilot plugin marketplace add tiaanduplessis/anyforge-copilot-plugin
copilot plugin install anyforge@anyforge
```

Start a session and ask about your AnyForge work. The first AnyForge tool call
opens the AnyForge sign-in in your browser.

### VS Code

1. Add the marketplace to your settings:

   ```json
   "chat.plugins.marketplaces": ["tiaanduplessis/anyforge-copilot-plugin"]
   ```

2. Open **Customize** in the Chat side panel, search for **AnyForge** and
   choose **Install**.
3. Start the `anyforge` MCP server when VS Code asks, and sign in.

### For a whole organization

Enterprise managed settings can require the plugin for everyone, so members
cannot turn it off. A repository can turn it on for everyone who works in it
with `.github/copilot/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "anyforge": {
      "source": { "source": "github", "repo": "tiaanduplessis/anyforge-copilot-plugin" }
    }
  },
  "enabledPlugins": {
    "anyforge@anyforge": true
  }
}
```

If your organization limits MCP servers to a registry, add AnyForge
(`ai.anyforge/anyforge` in the MCP registry) to the allowlist. The "MCP servers
in Copilot" policy must be on for Copilot Business and Enterprise.

## Copilot cloud agent and code review

Copilot's cloud agent and code review cannot sign in to an MCP server with
OAuth, so this plugin's server does not work there. Configure AnyForge in the
repository's Copilot settings instead, with an AnyForge service-account key in
a `COPILOT_MCP_` secret and a read-only tool allowlist. The
[AnyForge guide](https://anyforge.ai/docs?page=github-copilot) has the exact
configuration.

## What AnyForge reads from your repository

AnyForge's own agents (crews, Code Studio and the PR reviewer) read your
Copilot files too: `.github/copilot-instructions.md`, the
`.github/instructions/*.instructions.md` files by their `applyTo` globs, and
skills in `.github/skills/`. You do not need to restate your conventions for
AnyForge.

## License

Apache-2.0
