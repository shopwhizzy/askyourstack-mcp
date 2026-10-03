# SudoWhizzy MCP server

**Let Claude, ChatGPT, Cursor or Claude Code manage your own Linux servers, with approvals for anything risky.**

SudoWhizzy is a hosted, remote MCP server. Install a small agent on your server with one line, add your private SudoWhizzy MCP address to your AI client, and ask in plain words: set up a raw VPS, install Magento or WordPress, fix a 500 error, take backups, clean up malware, harden the server or move a site to a new one.

- Website: https://sudowhizzy.com
- Docs: https://sudowhizzy.com/docs
- Security model: https://sudowhizzy.com/security
- Prompt library: https://sudowhizzy.com/prompts

> This repository holds the listing and documentation for the SudoWhizzy MCP server. The service itself is hosted at sudowhizzy.com; there is nothing to build or run locally.

## How it works

1. **Connect your server.** Sign up free at [sudowhizzy.com](https://sudowhizzy.com/login?mode=signup), name your server and paste the one-line install as root. The agent dials out to sudowhizzy.com over HTTPS: it opens no port, and no SSH key or password is stored anywhere.
2. **Add SudoWhizzy to your AI client** with your private MCP address from the dashboard (see below).
3. **Ask.** Your AI looks at the server first, explains its plan, and runs the work through SudoWhizzy's tools. Anything destructive waits for your approval.

## Connect your AI client

Your MCP address looks like `https://sudowhizzy.com/mcp/YOUR-ADDRESS`. Keep it private, and make a new one in the dashboard if it leaks.

**Claude (claude.ai and Claude Desktop):** Settings, Connectors, Add custom connector. Name it SudoWhizzy, paste the address and leave the OAuth fields empty. [Guide](https://sudowhizzy.com/claude)

**Claude Code:**

```bash
claude mcp add --transport http sudowhizzy https://sudowhizzy.com/mcp/YOUR-ADDRESS
```

[Guide](https://sudowhizzy.com/claude-code)

**Cursor** (`mcp.json`):

```json
{
  "mcpServers": {
    "sudowhizzy": { "url": "https://sudowhizzy.com/mcp/YOUR-ADDRESS" }
  }
}
```

[Guide](https://sudowhizzy.com/cursor)

**ChatGPT:** Settings, Connectors, turn on developer mode, then add a custom connector with your address and no authentication. [Guide](https://sudowhizzy.com/chatgpt)

Any other client that supports remote MCP servers over streamable HTTP works the same way.

## Tools

| Tool | What it does | Plan |
|---|---|---|
| `list_servers` | The servers on the account: online, safety mode, paused | Free |
| `server_facts` | Distro, CPU, memory, disk, package manager, services, ports, Magento and WordPress installs | Free |
| `get_playbook` | Step-by-step guides for the AI (see Playbooks) | Free |
| `health_report` | Disk and inodes, memory, load, failed services, certificates, security updates, reboot needed | Free |
| `list_sites` | Magento and WordPress sites: root, owner, version, database (passwords never shown) | Free |
| `read_file` | Read a file | Free |
| `run` | Run a shell command as root; read-only commands on Free | Free / Starter |
| `list_jobs`, `job_output` | Follow background jobs, reading only new output | Free |
| `list_snapshots` | Snapshots on the server | Free |
| `start_job`, `stop_job` | Long commands (installs, composer, imports) in the background, surviving the chat | Starter |
| `write_file` | Create or replace a file, keeping the previous version | Starter |
| `snapshot`, `rollback` | Save folders and databases, write them back; a rollback can itself be undone | Starter |
| `db_query` | One SQL statement with the site's own credentials; reads in a read-only transaction | Starter |
| `magento` | `bin/magento` as the site's owner | Starter |
| `wp` | `wp-cli` as the site's owner | Starter |
| `db_dump` | Dump a site's database on its server, with its own credentials | Starter |
| `transfer`, `transfer_close` | Copy a folder directly between two of your servers for a migration | Starter |
| `malware_scan` | Backdoors, PHP in uploads, disguised PHP, injected scripts in the database, suspicious cron | Pro |

## Playbooks

Served as MCP prompts and through `get_playbook`: `server-setup`, `magento`, `wordpress`, `hardening`, `backups`, `site-checkup`, `malware-cleanup`, `migrate`. The AI fetches the right one on its own for tasks like a fresh Magento install or a hacked site.

## Safety

- **Every call is sorted by risk.** Reads run at once. Changes run after `/etc` is saved. Destructive actions (deleting your data, dropping databases, SSH, sudo and firewall changes, reboots) wait for your approval. Anything touching the agent's own key is blocked.
- **Approvals the AI cannot fake.** You approve signed in on sudowhizzy.com; an approval runs once, only with the exact arguments you saw, within 30 minutes. Text planted in a log or web page cannot approve anything.
- **Modes and kill switch.** Each server is read-only, normal or full trust. Pause a server or the whole account in one click; disconnecting revokes the agent.
- **Undo.** `/etc` snapshots before changes, previous versions of every overwritten file, snapshots and rollback.
- **Secrets stay on the server.** Database credentials are read from the site's own config and never reach the AI. Your MCP address is stored only as a hash.
- **Signed agent updates** and a full audit log of every call.

More at https://sudowhizzy.com/security

## Supported servers

Linux with systemd (Debian, Ubuntu, Rocky Linux, AlmaLinux, RHEL and similar), x86_64 or ARM64, from any provider. A fresh VPS or a server already running a shop or site.

## Pricing

Free: one server, read-only, 50 tool calls a day. Paid plans add changes, approvals and snapshots (Starter, 1 server), the malware scan and site checks every 15 minutes (Pro, 5 servers; Agency, 25 servers). Current prices: https://sudowhizzy.com/#pricing. Your AI client is billed separately by its own provider.

## Support

hello@sudowhizzy.com · [Terms](https://sudowhizzy.com/terms) · [Privacy](https://sudowhizzy.com/privacy)

SudoWhizzy is operated by SudoWhizzy Lda, Portugal. The SudoWhizzy service and agent are proprietary; this README may be quoted in MCP directories and listings.
