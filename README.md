# AskYourStack MCP server

**Let Claude, ChatGPT, Cursor or Claude Code manage your own Linux servers, with approvals for anything risky.**

AskYourStack is a hosted, remote MCP server. Install a small agent on your server with one line, add your private AskYourStack MCP address to your AI client, and ask in plain words: set up a raw VPS, install Magento or WordPress, fix a 500 error, take backups, clean up malware, harden the server or move a site to a new one. For SEO it reads Google Search Console, Google Analytics 4, Core Web Vitals, on-page audits and your structured data next to what Googlebot really fetches in your server logs, and fixes the causes on the server.

- Website: https://askyourstack.com
- Docs: https://askyourstack.com/docs
- Security model: https://askyourstack.com/security
- Prompt library: https://askyourstack.com/prompts

> This repository holds the listing and documentation for the AskYourStack MCP server. The service itself is hosted at askyourstack.com; there is nothing to build or run locally.

## How it works

1. **Connect your server.** Sign up free at [askyourstack.com](https://askyourstack.com/login?mode=signup), name your server and paste the one-line install as the Linux user who owns your sites (the agent then manages only that user's sites and files), or as root when it should run the whole server. The agent dials out to askyourstack.com over HTTPS: it opens no port, and no SSH key or password is stored anywhere.
2. **Add AskYourStack to your AI client** with your private MCP address from the dashboard (see below).
3. **Ask.** Your AI looks at the server first, explains its plan, and runs the work through AskYourStack's tools. Anything destructive waits for your approval.

## Connect your AI client

Two ways in:

- **Sign in (OAuth):** clients that support sign-in for remote MCP servers connect to `https://askyourstack.com/mcp`. You sign in to AskYourStack, see which app is asking and allow it. Each app gets its own access, which you can end under Security in the dashboard.
- **Private address:** `https://askyourstack.com/mcp/YOUR-ADDRESS`, from the dashboard, for clients without sign-in. Keep it private, and make a new one in the dashboard if it leaks. The examples below use it.

The tool and playbook catalogue is public at `https://askyourstack.com/mcp/catalog`.

**Claude (claude.ai and Claude Desktop):** Settings, Connectors, Add custom connector. Name it AskYourStack, paste the address and leave the OAuth fields empty. [Guide](https://askyourstack.com/claude)

**Claude Code:**

```bash
claude mcp add --transport http sudowhizzy https://askyourstack.com/mcp/YOUR-ADDRESS
```

[Guide](https://askyourstack.com/claude-code)

**Cursor** (`mcp.json`):

```json
{
  "mcpServers": {
    "sudowhizzy": { "url": "https://askyourstack.com/mcp/YOUR-ADDRESS" }
  }
}
```

[Guide](https://askyourstack.com/cursor)

**ChatGPT:** Settings, Connectors, turn on developer mode, then add a custom connector with your address and no authentication. [Guide](https://askyourstack.com/chatgpt)

Any other client that supports remote MCP servers over streamable HTTP works the same way.

## Tools

| Tool | What it does | Plan |
|---|---|---|
| `overview` | Where everything stands in one call: plan, servers and their sites, open problems and warnings from the monitoring, approvals waiting, connected Google properties, saved notes and recent work | Free |
| `propose_plan` | Ask approval once for a job with several risky steps: the user sees every risky step and approves them together; each then runs once, exactly as listed | Free |
| `wait_for_approval` | After giving the user an approval, plan or change-window link, wait in the same turn until they decide, then carry on without them returning to the chat | Starter |
| `save_note`, `forget_note` | Short notes about a server or the account that the next chat sees in `overview` (never passwords: those are refused) | Free |
| `log_work` | A plain summary of a finished task, for the owner's dashboard and the next chat | Free |
| `list_servers` | The servers on the account: online, safety mode, paused | Free |
| `server_facts` | Distro, CPU, memory, disk, package manager, services, ports, Magento and WordPress installs | Free |
| `get_playbook` | Step-by-step guides for the AI (see Playbooks) | Free |
| `health_report` | Disk and inodes, memory, load, failed services, certificates, security updates, reboot needed, backups (age and rhythm), out-of-memory kills, PHP out of workers, database connection limit; on Pro and Agency also software with a known vulnerability | Free |
| `list_sites` | The sites on the server: Magento 2, OpenMage (Magento 1), WordPress and WooCommerce, PrestaShop, Shopware 6, Drupal, Joomla, OpenCart and Laravel, found from the web server's own configuration (Plesk, CloudPanel, RunCloud, DirectAdmin and plain layouts) and in Docker containers: kind, root, domains, access logs, owner, version, database (passwords never shown), its console | Free |
| `logs` | A log by name (web-error, php, magento, wordpress, database, system, mail, auth, or any file) for a time window, with repeated errors counted once, a sample of each and the latest lines | Free |
| `read_file` | Read a file | Free |
| `run` | Run a shell command as the agent's user (root, or the site's own user); read-only commands on Free | Free / Starter |
| `list_jobs`, `job_output` | Follow background jobs, reading only new output | Free |
| `list_snapshots` | Snapshots on the server | Free |
| `start_job`, `stop_job` | Long commands (installs, composer, imports) in the background, surviving the chat | Starter |
| `write_file` | Create or replace a file, keeping the previous version | Starter |
| `snapshot`, `rollback` | Save folders and databases, write them back; a rollback can itself be undone | Starter |
| `db_query` | One SQL statement with the site's own credentials; reads in a read-only transaction | Starter |
| `magento` | `bin/magento` as the site's owner | Starter |
| `wp` | `wp-cli` as the site's owner | Starter |
| `site_console` | Run a site's own command line tool as its owner, also inside a Docker container: bin/console (Shopware 6, PrestaShop), artisan (Laravel), drush (Drupal), Joomla's cli. Lists run straight away; uninstalling, database resets and arbitrary code need approval | Starter |
| `connected_sites` | Sites connected without a server (a WordPress on any host, through its REST API with an application password, or a WhizzyCommerce shop through the shop's own connector): name, mode, content types | Free |
| `content_list`, `content_get` | Read a connected site's posts, pages, media, categories, tags and other content types | Free |
| `content_save`, `content_upload`, `content_trash`, `content_undo` | Write drafts, edit, upload images, trash and undo on a connected site, inside the mode its owner chose (read-only, drafts only, publish too); what a change replaced is kept 90 days | Starter |
| `whizzy_tools` | What a connected WhizzyCommerce shop lets the AI do right now: orders and shipping, catalogue and imports, prices, sales, coupons and gift cards, customers, translations, menu, redirects and SEO, settings, routines, reports and design (sections, colours and fonts, the merchant's own themes). Everything by default; the merchant can narrow it in their WhizzyCommerce dashboard. Search for one capability to read its whole input schema | Free |
| `whizzy_call` | Run one capability of a connected WhizzyCommerce shop. Reads answer at once; most changes come back as a preview with a link the merchant confirms in their own dashboard, and their outcome can be read afterwards; page layouts and own themes are drafts the merchant publishes; colours, fonts and theme settings apply at once with an Undo in the shop's change log. Nothing the shop answers is stored | Free (reads), Starter |
| `keyword_volume` | Google search volume, 12-month history, CPC and competition for up to 200 keywords in a country and language (metered: a monthly allowance per plan) | Starter, SEO Starter |
| `keyword_ideas` | Up to 100 keyword suggestions around a seed, with volume, keyword difficulty and intent | Starter, SEO Starter |
| `serp_check` | The live Google results for a keyword, desktop or mobile, with the user's site marked and the page features (AI overview, snippets, people also ask) | Starter, SEO Starter |
| `domain_keywords` | What a domain ranks for, with position, volume, estimated visits and page, and its competitors | Starter, SEO Starter |
| `bing_sites` | The sites in the user's Bing Webmaster Tools account (connected on the SEO page by signing in at Bing or with an API key) and which the AI may read | Starter, SEO Starter |
| `bing_performance` | Bing search performance: daily impressions and clicks, the last 28 days against the 28 before, top queries and pages, the pages of one query or the queries of one page | Starter, SEO Starter |
| `bing_crawl` | Bingbot's crawl: pages crawled and in the index, errors, crawl issues per address, one address inspected | Starter, SEO Starter |
| `bing_keywords` | Keyword research from Bing's own data: searches on Bing for a keyword, monthly history, related keywords | Starter, SEO Starter |
| `bing_backlinks` | Inbound links Bing knows: the site's pages with the most links, and who links to one page with which anchor text | Starter, SEO Starter |
| `bing_sitemaps` | The sitemaps Bing knows for a site with status and counts | Starter, SEO Starter |
| `bing_submit` | Submit up to 500 pages or a sitemap to Bing, with the quota left | Starter, SEO Starter |
| `disavow_file` | The disavow file for Google's disavow links tool, from the user's backlink data (Ahrefs, Semrush, SE Ranking, Bing), merged with the file Google already has and checked against Google's format and limits; the user uploads it (Google has no API for it) | Starter, SEO Starter |
| `cf_zones` | The Cloudflare zones the user connected with an API token (Sites page), with status, plan, name servers and the connection's mode | Starter |
| `cf_dns_list` | List the DNS records of a Cloudflare zone, filtered by type, name or search (part of a name or content), each with its id. | Starter |
| `cf_dns_add` | Add a DNS record to a Cloudflare zone: a new subdomain, a mail record or a verification text. | Starter |
| `cf_dns_change` | Change one DNS record of a Cloudflare zone, by record_id from cf_dns_list (or name plus type when they match one record): its content, TTL, proxy, priority or name. | Starter |
| `cf_dns_remove` | Remove one DNS record of a Cloudflare zone, by record_id from cf_dns_list (or name plus type when they match one record). | Starter |
| `cf_purge_cache` | Clear a zone's Cloudflare cache, everything or given addresses | Starter |
| `cf_firewall_list` | List the custom firewall rules and rate limits of a Cloudflare zone, each with its id, action and expression. | Starter |
| `cf_firewall_add` | Add one firewall rule or rate limit to a Cloudflare zone. | Starter |
| `cf_firewall_remove` | Remove one custom firewall rule or rate limit from a Cloudflare zone, by rule_id from cf_firewall_list. | Starter |
| `cf_settings` | Under attack mode, security level, development mode, browser check; in the fullest mode SSL mode, always use HTTPS and minimum TLS | Starter |
| `cf_analytics` | Traffic through Cloudflare: requests, cached share, visitors, threats, bandwidth and status codes per day, top countries, requests per hour, a sample of what the firewall stopped in the last 24 hours | Starter |
| `cf_undo` | Undo a Cloudflare change made through cf_dns, cf_firewall or cf_settings, or list them | Starter |
| `redirects_find` | Find the paths of a site on the user's server that answer 404: from the crawler hits in its access logs (the last hours, default 168) and, when Search Console is connected, pages that had clicks in the last 90 days and are gone now. | Starter |
| `redirects_list` | The custom redirects a site holds: Magento's url_rewrite table, or WordPress with the Redirection or Rank Math plugin. | Starter |
| `redirects_apply` | Write redirects (from a path, to a path or full address, code 301 or 302) into the site's redirect store: Magento url_rewrite, or the WordPress Redirection or Rank Math plugin. | Starter |
| `redirects_undo` | Remove the redirects one redirects_apply batch wrote. | Starter |
| `redirects_history` | The batches of redirects written on a site in the last 90 days, each with its redirects and whether it was undone. | Starter |
| `convert_images` | WebP or AVIF copies next to a site's JPEG and PNG uploads, made as the site's owner with the server's own encoder; originals untouched, a second run does only new files | Starter |
| `list_dumps` | The database dumps kept on a server, by kind, size and age | Free |
| `db_restore` | Load a dump back into a site's database with its own credentials; the database as it is now is dumped first, so the restore can be undone | Starter |
| `seo_fields_list` | List the SEO titles, meta descriptions or image alt texts of a WordPress site (Yoast SEO, Rank Math, SEOPress or The SEO Framework) or a Magento 2 shop (products, categories, CMS pages and product images at the default store scope), with their current values. | Starter |
| `seo_fields_apply` | Set SEO titles, meta descriptions or image alt texts on a WordPress site or a Magento 2 shop, up to 30 fields a call, by the ids seo_fields_list gave. | Starter |
| `seo_fields_undo` | Put back the values a seo_fields_apply batch replaced (kept 90 days). | Starter |
| `seo_fields_history` | The batches of SEO title, description and alt text changes made on a site in the last 90 days, with what each changed and whether it was undone. | Starter |
| `db_dump` | Dump a site's database on its server, with its own credentials | Starter |
| `transfer`, `transfer_close` | Copy a folder directly between two of your servers for a migration | Starter |
| `malware_scan` | Backdoors, PHP in uploads, disguised PHP, injected scripts in the database, suspicious cron, plus a maintained rule set: known skimmer and backdoor signatures, missing Magento security patches, vulnerable modules, WordPress plugins and themes, exposed .git and .env, config copies, crypto miners and rootkit signs on the server | Pro |
| `crawl_report` | What Googlebot, Bingbot, AI crawlers and SEO tools really fetched, from the access logs: errors, crawl waste, fake Googlebots | Free |
| `site_audit` | Crawl a whole site from the server it runs on (up to 5,000 pages, no CDN in the way), in the background: status, redirects, titles, canonicals, noindex, word counts, links, sitemap coverage for every page | Pro |
| `site_audit_report` | Read a site audit by issue (broken links, redirect chains, repeated titles, duplicate and thin pages, sitemap problems), by page, or against the run before | Pro |
| `seo_history` | Daily Search Console, Analytics and Core Web Vitals figures stored once the user switches history on, with alerts: clicks fell, a top page left the index, a vital turned poor | Starter |
| `indexnow_submit` | Tell Bing, Yandex and the other IndexNow search engines which pages are new, changed or gone | Starter |
| `gsc_properties` | The Google Search Console properties the user picked (connected read-only in the dashboard) | Free |
| `gsc_performance_overview` | How the site is doing: the period against the one before, daily trend, biggest winning and losing queries and pages | Free |
| `gsc_search_analytics` | Clicks, impressions, CTR and position by query, page, country, device or date, with filters and two-period comparison | Free |
| `gsc_inspect_url` | Google's URL Inspection for up to 20 URLs: indexed or not and why, last crawl, Google's canonical | Free |
| `gsc_sitemaps` | Submitted sitemaps: errors, warnings, last download, URLs per type | Free |
| `core_web_vitals` | Real-user LCP, INP, CLS, FCP and TTFB from the Chrome UX Report for a page or site, LCP broken into parts, 6 months of weekly history, optional Lighthouse test | Starter |
| `validate_schema` | Structured data (JSON-LD and microdata) checked against schema.org and Google's rich-result requirements, live URL or pasted HTML | Starter |
| `google_updates` | Google core, spam and Discover updates since 2021 with their rollout dates, to line up with traffic changes | Free |
| `gsc_opportunities` | What to work on first, ranked by clicks at stake: queries just off the top places, page-one pages with a low CTR, pages losing clicks, and two pages competing for one query, each with what to do | Free |
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
| `robots_check` | robots.txt tests the way Google reads them, for Googlebot, Bingbot and AI crawlers | Free |
| `llms_txt` | Builds an llms.txt for a site (the file AI assistants read): name, summary and its key pages from the sitemap with their own titles and descriptions; writes nothing | Starter |
| `sitemap_check` | Sitemap audit (indexes, limits, lastmod) with a sample of URLs checked for status, redirects, noindex and canonical | Pro |
| `job_output` | Status (running, finished, ended), exit code and output of a background job. | Free |
| `stop_job` | Stop a running background job and everything it started. | Starter |
| `rollback` | Write a snapshot's files back (all of it, or only some paths). | Starter |
| `forget_note` | Delete a saved note that is wrong or no longer true (its id is in overview). | Free |
| `merchant_accounts` | The Google Merchant Center accounts the user's connected Google account can see (sub-accounts of a multi-client account included), with their ids. | Starter, SEO Starter |
| `merchant_issues` | Why products are not showing in Google Shopping ads and free listings: per destination and country the number of products active, pending, disapproved and expiring, the account's own issues (suspensions, missing settings, website claims), and the product issues grouped by kind with how many products each hits, how severe it is, Google's explanation and help link. | Starter, SEO Starter |
| `merchant_products` | Products in Merchant Center with their issues: offer id, title, link, and each issue with severity, the attribute concerned, where it applies and Google's explanation. | Starter, SEO Starter |
| `content_get` | Read one item of a connected site in full, as its editor would see it: title, content (the stored source), status, address and what else it has. | Free |
| `content_upload` | Add an image to a connected site's media library (WordPress) or files (Shopify) from a public address (jpeg, png, gif, webp or avif, up to 15 MB), with its alt text and title. | Starter |
| `content_trash` | Move a post, page or other item of a WordPress site to its trash (WordPress keeps it 30 days; content_undo brings it back), or remove a URL redirect of a Shopify store (content_undo makes it again). | Starter |
| `content_undo` | Undo a change made through content_save, content_upload or content_trash on a connected site: an edit gets its old values back, a created item goes to the trash, a trashed one comes back. | Starter |
| `transfer_close` | Close the copy links between servers (removes the temporary keys). | Starter |

Search Console history: all 16 months Google keeps, on every plan (Free reads one property). Google Analytics 4: Pro 10 properties and 90 days, Agency 25 and all history. Core Web Vitals: 100 checks a day on Starter, 300 on Pro, 1,000 on Agency. Agency can connect up to 3 Google accounts (clients' own).

## Playbooks

Served as MCP prompts and through `get_playbook`: `server-setup`, `magento`, `wordpress`, `hardening`, `backups`, `site-checkup`, `malware-cleanup`, `migrate`, `bot-attack`, `seo-technical`, `publish-content`, `performance`, `updates`, `site-down`, `add-site`, `staging`, `restore`, `magento-upgrade`, `email`, `woocommerce`, `prestashop`, `shopware`, `drupal`, `laravel`, `other-shops` (Joomla, OpenCart, OpenMage), `seo-dashboard`, `whizzycommerce`, `ghost`. The AI fetches the right one on its own for tasks like a fresh Magento install or a hacked site.

## Safety

- **Every call is sorted by risk.** Reads run at once. Changes run after `/etc` is saved. Destructive actions (deleting your data, dropping databases, SSH, sudo and firewall changes, reboots) wait for your approval. Anything touching the agent's own key is blocked.
- **Approvals the AI cannot fake.** You approve signed in on askyourstack.com; an approval runs once, only with the exact arguments you saw, within 30 minutes. Text planted in a log or web page cannot approve anything. The approval page says in plain words what the action does, what could go wrong and how to undo it, and before an approved delete of database tables or whole folders a copy is saved first when the command names them plainly.
- **Modes and kill switch.** Each server is read-only, normal or full trust. Pause a server or the whole account in one click; disconnecting revokes the agent.
- **Undo.** `/etc` snapshots before changes, previous versions of every overwritten file, snapshots and rollback.
- **Secrets stay on the server.** Database credentials are read from the site's own config and never reach the AI. Your MCP address is stored only as a hash.
- **Signed agent updates** and a full audit log of every call.

More at https://askyourstack.com/security

## Supported servers

Linux with systemd (Debian, Ubuntu, Rocky Linux, AlmaLinux, RHEL and similar), x86_64 or ARM64, from any provider. A fresh VPS or a server already running a shop or site.

## Pricing

Free: one server, read-only, 50 tool calls a day, and Google Search Console for one property with the opportunities report. Paid plans add changes, approvals and snapshots and the SEO tools with Google Search Console (Starter, 1 server, 3 properties), the malware scan, site checks every 15 minutes, Google Analytics 4, on-page and technical SEO tools and longer history (Pro, 5 servers, 10 properties; Agency, 25 servers, 25 properties, up to 3 Google accounts). Current prices: https://askyourstack.com/#pricing. Every tool with what it does and its plan: https://askyourstack.com/docs/tools. Your AI client is billed separately by its own provider.

## Support

info@askyourstack.com · [Terms](https://askyourstack.com/terms) · [Privacy](https://askyourstack.com/privacy)

AskYourStack is operated by Whizzy Digital Solutions Lda, Portugal. The agent installed on your server is open source (Apache-2.0): https://github.com/shopwhizzy/askyourstack-agent, with a script that checks each release against its source. The hosted service is proprietary; this README may be quoted in MCP directories and listings.
