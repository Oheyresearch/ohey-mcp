# Ohey MCP server

Ohey lists hosts that serve web malware: compromised sites, ClickFix and fake
update lures, skimmers, and the C2 and distribution servers behind them. This
server gives AI agents the Ohey API as tools.

```
https://ohey.ca/mcp
```

It's a remote server (Streamable HTTP). You sign in with your Ohey account, so
there is no API key to copy. It needs an account with API access; ask for one
at [ohey.ca/contact](https://ohey.ca/contact/).

## Connect

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

## Tools

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

## Access and revoking it

Approving a client creates an API key named after it, under Account in the
[Ohey portal](https://ohey.ca/portal/). Tool calls are rate limited and
counted against that key like any API request. Revoke the key to disconnect
the client.

The full API, with every response field, is documented at
[ohey.ca/docs](https://ohey.ca/docs/).

## Security

Report security issues to hey@ohey.ca. See
[security.txt](https://ohey.ca/.well-known/security.txt).
