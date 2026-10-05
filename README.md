# SudoWhizzy MCP server

**Let Claude, ChatGPT, Cursor or Claude Code manage your own Linux servers, with approvals for anything risky.**

SudoWhizzy is a hosted, remote MCP server. Install a small agent on your server with one line, add your private SudoWhizzy MCP address to your AI client, and ask in plain words: set up a raw VPS, install Magento or WordPress, fix a 500 error, take backups, clean up malware, harden the server or move a site to a new one. For SEO it reads Google Search Console, Google Analytics 4, Core Web Vitals, on-page audits and your structured data next to what Googlebot really fetches in your server logs, and fixes the causes on the server.

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

Two ways in:

- **Sign in (OAuth):** clients that support sign-in for remote MCP servers connect to `https://sudowhizzy.com/mcp`. You sign in to SudoWhizzy, see which app is asking and allow it. Each app gets its own access, which you can end under Security in the dashboard.
- **Private address:** `https://sudowhizzy.com/mcp/YOUR-ADDRESS`, from the dashboard, for clients without sign-in. Keep it private, and make a new one in the dashboard if it leaks. The examples below use it.

The tool and playbook catalogue is public at `https://sudowhizzy.com/mcp/catalog`.

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
| `overview` | Where everything stands in one call: plan, servers and their sites, open problems and warnings from the monitoring, approvals waiting, connected Google properties, saved notes and recent work | Free |
| `propose_plan` | Ask approval once for a job with several risky steps: the user sees every risky step and approves them together; each then runs once, exactly as listed | Free |
| `save_note`, `forget_note` | Short notes about a server or the account that the next chat sees in `overview` (never passwords: those are refused) | Free |
| `log_work` | A plain summary of a finished task, for the owner's dashboard and the next chat | Free |
| `list_servers` | The servers on the account: online, safety mode, paused | Free |
| `server_facts` | Distro, CPU, memory, disk, package manager, services, ports, Magento and WordPress installs | Free |
| `get_playbook` | Step-by-step guides for the AI (see Playbooks) | Free |
| `health_report` | Disk and inodes, memory, load, failed services, certificates, security updates, reboot needed, backups (age and rhythm), out-of-memory kills, PHP out of workers, database connection limit; on Pro and Agency also software with a known vulnerability | Free |
| `list_sites` | Magento and WordPress sites, found from the web server's own configuration (Plesk, CloudPanel, RunCloud, DirectAdmin and plain layouts): root, domains, access logs, owner, version, database (passwords never shown) | Free |
| `logs` | A log by name (web-error, php, magento, wordpress, database, system, mail, auth, or any file) for a time window, with repeated errors counted once, a sample of each and the latest lines | Free |
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
| `seo_fields` | WordPress SEO titles, meta descriptions and image alt texts (Yoast SEO, Rank Math, SEOPress, The SEO Framework): list what is there or missing, change in small batches with a preview of old against new, undo a batch | Starter |
| `db_dump` | Dump a site's database on its server, with its own credentials | Starter |
| `transfer`, `transfer_close` | Copy a folder directly between two of your servers for a migration | Starter |
| `malware_scan` | Backdoors, PHP in uploads, disguised PHP, injected scripts in the database, suspicious cron, plus a maintained rule set: known skimmer and backdoor signatures, missing Magento security patches, vulnerable modules, WordPress plugins and themes, exposed .git and .env, config copies, crypto miners and rootkit signs on the server | Pro |
| `crawl_report` | What Googlebot, Bingbot, AI crawlers and SEO tools really fetched, from the access logs: errors, crawl waste, fake Googlebots | Free |
| `site_audit` | Crawl a whole site from the server it runs on (up to 5,000 pages, no CDN in the way), in the background: status, redirects, titles, canonicals, noindex, word counts, links, sitemap coverage for every page | Pro |
| `site_audit_report` | Read a site audit by issue (broken links, redirect chains, repeated titles, duplicate and thin pages, sitemap problems), by page, or against the run before | Pro |
| `seo_history` | Daily Search Console, Analytics and Core Web Vitals figures stored once the user switches history on, with alerts: clicks fell, a top page left the index, a vital turned poor | Starter |
| `indexnow_submit` | Tell Bing, Yandex and the other IndexNow search engines which pages are new, changed or gone | Starter |
| `gsc_properties` | The Google Search Console properties the user picked (connected read-only in the dashboard) | Starter |
| `gsc_performance_overview` | How the site is doing: the period against the one before, daily trend, biggest winning and losing queries and pages | Starter |
| `gsc_search_analytics` | Clicks, impressions, CTR and position by query, page, country, device or date, with filters and two-period comparison | Starter |
| `gsc_inspect_url` | Google's URL Inspection for up to 20 URLs: indexed or not and why, last crawl, Google's canonical | Starter |
| `gsc_sitemaps` | Submitted sitemaps: errors, warnings, last download, URLs per type | Starter |
| `core_web_vitals` | Real-user LCP, INP, CLS, FCP and TTFB from the Chrome UX Report for a page or site, LCP broken into parts, 6 months of weekly history, optional Lighthouse test | Starter |
| `validate_schema` | Structured data (JSON-LD and microdata) checked against schema.org and Google's rich-result requirements, live URL or pasted HTML | Starter |
| `google_updates` | Google core, spam and Discover updates since 2021 with their rollout dates, to line up with traffic changes | Starter |
| `gsc_cannibalization` | Searches where two or more of the site's pages compete in Google, with clicks, impressions and position per page | Pro |
| `ga4_properties` | The Google Analytics 4 properties the user picked, with a tracking health check (data flowing, key events, retention) | Pro |
| `ga4_overview` | Sessions, users, engagement, conversions and revenue against the period before, by channel, with organic search's share | Pro |
| `ga4_landing_pages` | Landing pages with engagement, bounce rate, conversions and revenue, per channel and compared with an earlier period | Pro |
| `ga4_ai_traffic` | Visits from ChatGPT, Perplexity, Gemini, Claude, Copilot and other AI assistants, and the pages they land on | Pro |
| `ga4_report` | Any GA4 report: new vs returning, site search terms, devices, countries, sources, e-commerce | Pro |
| `ga4_realtime` | Active users in the last 30 minutes by page, country and device | Pro |
| `page_audit` | On-page audit of up to 10 URLs on any site: redirects, headers, title, description, robots, canonical, hreflang, headings, Open Graph, alt text, content length, issues | Pro |
| `page_links` | Every link on a page with anchor text and nofollow; finds broken and redirected links | Pro |
| `page_content` | A page's main text without navigation and footers, as markdown | Pro |
| `robots_check` | robots.txt tests the way Google reads them, for Googlebot, Bingbot and AI crawlers | Pro |
| `sitemap_check` | Sitemap audit (indexes, limits, lastmod) with a sample of URLs checked for status, redirects, noindex and canonical | Pro |

Search Console history by plan: Starter the last 30 days, Pro 90 days, Agency all 16 months Google keeps. Google Analytics 4: Pro 10 properties and 90 days, Agency 25 and all history. Core Web Vitals: 100 checks a day on Starter, 300 on Pro, 1,000 on Agency. Agency can connect up to 3 Google accounts (clients' own).

## Playbooks

Served as MCP prompts and through `get_playbook`: `server-setup`, `magento`, `wordpress`, `hardening`, `backups`, `site-checkup`, `malware-cleanup`, `migrate`, `bot-attack`, `seo-technical`, `publish-content`, `performance`, `updates`, `site-down`, `add-site`, `staging`, `restore`, `magento-upgrade`, `email`. The AI fetches the right one on its own for tasks like a fresh Magento install or a hacked site.

## Safety

- **Every call is sorted by risk.** Reads run at once. Changes run after `/etc` is saved. Destructive actions (deleting your data, dropping databases, SSH, sudo and firewall changes, reboots) wait for your approval. Anything touching the agent's own key is blocked.
- **Approvals the AI cannot fake.** You approve signed in on sudowhizzy.com; an approval runs once, only with the exact arguments you saw, within 30 minutes. Text planted in a log or web page cannot approve anything. The approval page says in plain words what the action does, what could go wrong and how to undo it, and before an approved delete of database tables or whole folders a copy is saved first when the command names them plainly.
- **Modes and kill switch.** Each server is read-only, normal or full trust. Pause a server or the whole account in one click; disconnecting revokes the agent.
- **Undo.** `/etc` snapshots before changes, previous versions of every overwritten file, snapshots and rollback.
- **Secrets stay on the server.** Database credentials are read from the site's own config and never reach the AI. Your MCP address is stored only as a hash.
- **Signed agent updates** and a full audit log of every call.

More at https://sudowhizzy.com/security

## Supported servers

Linux with systemd (Debian, Ubuntu, Rocky Linux, AlmaLinux, RHEL and similar), x86_64 or ARM64, from any provider. A fresh VPS or a server already running a shop or site.

## Pricing

Free: one server, read-only, 50 tool calls a day. Paid plans add changes, approvals and snapshots and the SEO tools with Google Search Console (Starter, 1 server, 3 properties), the malware scan, site checks every 15 minutes, Google Analytics 4, on-page and technical SEO tools and longer history (Pro, 5 servers, 10 properties; Agency, 25 servers, 25 properties, up to 3 Google accounts). Current prices: https://sudowhizzy.com/#pricing. Your AI client is billed separately by its own provider.

## Support

hello@sudowhizzy.com · [Terms](https://sudowhizzy.com/terms) · [Privacy](https://sudowhizzy.com/privacy)

SudoWhizzy is operated by SudoWhizzy Lda, Portugal. The SudoWhizzy service and agent are proprietary; this README may be quoted in MCP directories and listings.
