# Ohey MCP servers

Ohey scans websites for injected malware: ClickFix and fake update lures,
skimmers, phishing kits, EtherHiding loaders, and the C2 and distribution
servers behind them. There are two remote MCP servers (Streamable HTTP).

| Server | URL | Sign-in |
| --- | --- | --- |
| Research | `https://ohey.ca/mcp/research` | None |
| Feed and API | `https://ohey.ca/mcp` | Ohey account with API access |

## Install

Both servers are remote Streamable HTTP servers, so there is nothing to
download. The examples add the research server; for the feed server, use
the name `ohey` and the URL `https://ohey.ca/mcp`, and the client opens
ohey.ca to sign in the first time.

Both are in the [MCP Registry](https://registry.modelcontextprotocol.io/)
as `ca.ohey/research` and `ca.ohey/ohey`.

### Claude

Web, desktop and mobile: Settings, Connectors, Add custom connector, then
paste the URL.

Claude Code:

```bash
claude mcp add --transport http ohey-research https://ohey.ca/mcp/research
```

### ChatGPT

Custom MCP servers need developer mode, which OpenAI offers on some plans
and has moved between settings pages; a Business or Enterprise admin may
have to allow custom connectors first.

1. Turn on developer mode in ChatGPT's settings.
2. Add a custom connector (or app), give it a name and paste the URL.
3. Set Authentication to No authentication for the research server, or
   OAuth for the feed server.
4. In a new chat, turn the connector on from the tools menu.

### OpenAI Codex

```bash
codex mcp add ohey-research --url https://ohey.ca/mcp/research
```

For the feed server, add it with `--url https://ohey.ca/mcp`, then run
`codex mcp login ohey` to sign in. Or in `~/.codex/config.toml`:

```toml
[mcp_servers.ohey-research]
url = "https://ohey.ca/mcp/research"
```

### Gemini CLI

```bash
gemini mcp add --transport http ohey-research https://ohey.ca/mcp/research
```

Or in `settings.json`, under `mcpServers`:

```json
"ohey-research": { "httpUrl": "https://ohey.ca/mcp/research" }
```

For the feed server, run `/mcp auth ohey` in the CLI to sign in.

### Cursor

In `~/.cursor/mcp.json`, or `.cursor/mcp.json` in a project:

```json
{
  "mcpServers": {
    "ohey-research": { "url": "https://ohey.ca/mcp/research" }
  }
}
```

### VS Code (GitHub Copilot)

```bash
code --add-mcp '{"name":"ohey-research","type":"http","url":"https://ohey.ca/mcp/research"}'
```

Or run MCP: Add Server, choose HTTP and paste the URL. The JSON form, in
`.vscode/mcp.json` or your user `mcp.json`:

```json
{
  "servers": {
    "ohey-research": { "type": "http", "url": "https://ohey.ca/mcp/research" }
  }
}
```

### Other clients

Any client that supports remote MCP servers over Streamable HTTP takes the
URL as is. Most read an `mcpServers` object with a `url` (some call the
field `serverUrl` or `httpUrl`). A client that only runs local stdio
servers can reach either one through
[mcp-remote](https://www.npmjs.com/package/mcp-remote):

```json
{
  "mcpServers": {
    "ohey-research": { "command": "npx", "args": ["-y", "mcp-remote", "https://ohey.ca/mcp/research"] }
  }
}
```

## Research server

The research Ohey publishes on [ohey.ca/threats](https://ohey.ca/threats/)
and [ohey.ca/research](https://ohey.ca/research/), as tools. Anyone can
connect, with no sign-in.

| Tool | What it does |
| --- | --- |
| `list_threats` | Every technique, with its page, a summary, site counts and its kits. |
| `get_threat` | A technique or kit: sites matched by month, how many were still infected when rescanned, WordPress and Joomla shares, common plugins, kits seen on the same sites, its EtherHiding contracts, its ClickFix command hosts and articles. |
| `list_research_articles` | Research articles, newest first. |
| `get_article` | One article's summary, key facts (hashes, active window, scale) and its indicators of compromise, as Markdown. |
| `search_clickfix_commands` | Hosts that ClickFix lure commands fetch from, by kit or host, with the latest command for each, defanged. |
| `search_etherhiding_contracts` | Contracts that compromised sites read their next stage from, by kit, chain or destination host. |
| `get_etherhiding_contract` | One contract's destinations over time. |
| `get_feed_stats` | Feed size, last week's additions and removals, and the capture archive. |

Counts update daily from Ohey's scans. Both search tools take `offset` to
page past `limit`, and `sort: "firstSeen"` for the newest first. A host
search that finds nothing returns a `coverage` note: the trackers list only
contract destinations and command hosts, so a miss means "not tracked", not
"clean". Checking any host against the full feed is the feed server's job.

Hosts and commands are live malware infrastructure. They come defanged
(`[.]`, `hxxp`), and results that carry commands include a `notice` for
the person reading. Refang or run them only in an isolated research
environment. See the [terms](https://ohey.ca/terms/#research-material).

## Feed and API server

Ohey's API as tools: look up any host, search the feed, read CVEs and scan
URLs. You sign in with your Ohey account, so there is no API key to copy. Ask
for an account with API access at [ohey.ca/contact](https://ohey.ca/contact/).

Connect it the same way as the research server, with `https://ohey.ca/mcp`
as the URL ([Install](#install)). Signing in opens ohey.ca; approve the
connection there and the client gets access. Clients register themselves
from the server's OAuth metadata, so there is no client ID to set.

### Tools

| Tool | What it does |
| --- | --- |
| `search_indicators` | Hosts in the feed, filtered by tag or family, with roles, families, tags and the reason each was admitted. |
| `get_host` | A host's feed entry, vulnerable software from its latest capture, its captures summary and DNS records. |
| `get_host_captures` | Each time the scanner loaded the host: when, what it returned and what matched. |
| `list_delisted` | Hosts that review excluded or took out of the feed, with the reason and who decided. |
| `get_vulnerability` | One CVE with its NVD, EPSS, KEV, GitHub and OSV data. |
| `list_vulnerabilities` | Published CVEs in order, or only WordPress plugin, theme and core CVEs. |
| `start_scan` | Loads a URL in Ohey's scanner and records what it finds. |
| `get_scan` | The scan result: whether the page is malicious, what matched, commands and clipboard writes it tried to plant, and suspected C2. |

The scan tools appear only when you tick "Scan URLs" while approving. Scans
count against your account.

### Access and revoking it

Approving a client creates an API key named after it, under Account in the
[Ohey portal](https://ohey.ca/portal/). Tool calls are rate limited and
counted against that key like any API request. Revoke the key to disconnect
the client.

The full API, with every response field, is documented at
[ohey.ca/docs](https://ohey.ca/docs/).

## Security

Report security issues to hey@ohey.ca. See
[security.txt](https://ohey.ca/.well-known/security.txt).
