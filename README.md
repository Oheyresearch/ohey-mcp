# Ohey MCP servers

Ohey scans websites for injected malware: ClickFix and fake update lures,
skimmers, phishing kits, EtherHiding loaders, and the C2 and distribution
servers behind them. There are two remote MCP servers (Streamable HTTP).

| Server | URL | Sign-in |
| --- | --- | --- |
| Research | `https://ohey.ca/mcp/research` | None |
| Feed and API | `https://ohey.ca/mcp` | Ohey account with API access |

## Research server

The research Ohey publishes on [ohey.ca/threats](https://ohey.ca/threats/),
as tools. Anyone can connect.

| Tool | What it does |
| --- | --- |
| `list_threats` | Every technique, with its page, a summary, site counts and its kits. |
| `get_threat` | A technique or kit: sites matched by month, how many were still infected when rescanned, WordPress and Joomla shares, common plugins, kits seen on the same sites, its EtherHiding contracts and articles. |
| `list_research_articles` | Research articles, newest first. |
| `search_etherhiding_contracts` | Contracts that compromised sites read their next stage from, by kit, chain or destination host. |
| `get_etherhiding_contract` | One contract's destinations over time. |
| `get_feed_stats` | Feed size, last week's additions and removals, and the capture archive. |

Counts update daily from Ohey's scans.

**Claude Code:**

```bash
claude mcp add --transport http ohey-research https://ohey.ca/mcp/research
```

## Feed and API server

Ohey's API as tools: look up any host, search the feed, read CVEs and scan
URLs. You sign in with your Ohey account, so there is no API key to copy. Ask
for an account with API access at [ohey.ca/contact](https://ohey.ca/contact/).

### Connect

**Claude:** Settings, Connectors, Add custom connector, then paste the URL.

**Claude Code:**

```bash
claude mcp add --transport http ohey https://ohey.ca/mcp
```

**Other clients:** add `https://ohey.ca/mcp` as a remote MCP server. The
client finds the sign-in pages from the server's OAuth metadata and registers
itself.

Signing in opens ohey.ca. Approve the connection there and the client gets
access.

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
