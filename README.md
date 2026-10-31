# firecrawl-and-exa-together

A Claude Code plugin marketplace with one plugin, **`firecrawl-exa`**. It connects
**[Firecrawl](https://docs.firecrawl.dev/mcp-server)** and **[Exa](https://exa.ai)** to Claude at the same time and adds
skills that send each web task to the tool that does it best: Exa to *find* things, Firecrawl to *fetch and work with*
pages.

## What's included

| Component | What it does |
|---|---|
| **MCP server `firecrawl`** | Firecrawl's hosted server, `https://mcp.firecrawl.dev/v2/mcp` (HTTP). Scrape, crawl, map, search, extract, browser interaction and parsing. Sends `Authorization: Bearer ${FIRECRAWL_API_KEY:-}` (empty when the variable is unset). |
| **MCP server `exa`** | Exa's hosted server, `https://mcp.exa.ai/mcp?client=claude-code-plugin` (HTTP). Semantic web search, code/company/people/paper search, page contents, and Exa Agent. |
| **Skill `firecrawl`** | Firecrawl CLI workflow: scrape, crawl, map, interact, monitor and parse. Uses the `firecrawl` CLI when installed, the MCP server otherwise. |
| **Skill `firecrawl-search`** | Web search through Firecrawl with optional full-page scraping of the results. |
| **Skill `exa-agent`** | Hands long, multi-step research tasks to Exa Agent (needs Exa sign-in or an API key). |
| **Skill `exa-search`** | Exa deep-research orchestrator: plans queries, runs Exa searches, reads sources and writes a cited answer. Upstream it is called `search`. |

Skills appear namespaced as `firecrawl-exa:<skill>`, and MCP servers as `plugin:firecrawl-exa:firecrawl` and
`plugin:firecrawl-exa:exa`.

## Install

Inside Claude Code:

```text
/plugin marketplace add /path/to/firecrawl-and-exa-together-plugin
/plugin install firecrawl-exa@firecrawl-and-exa-together
```

Or from a shell:

```bash
claude plugin marketplace add /path/to/firecrawl-and-exa-together-plugin
claude plugin install firecrawl-exa@firecrawl-and-exa-together
```

To try it for one session without installing:

```bash
claude --plugin-dir /path/to/firecrawl-and-exa-together-plugin
```

## Configuration

### Firecrawl API key

Get a key at [firecrawl.dev](https://www.firecrawl.dev/app/api-keys) and export it in the shell that **launches** Claude
Code:

```bash
export FIRECRAWL_API_KEY=fc-...   # e.g. in ~/.zshrc, then open a new terminal
claude
```

Claude Code expands `${FIRECRAWL_API_KEY:-}` into the `Authorization` header when it starts the server. Never paste the
key into the chat or commit it; `.env` is already in `.gitignore`.

Without a key the header is sent empty and Firecrawl falls back to its keyless tier: **scrape, search and parse only**,
rate-limited. Crawl, map, interact, extract and monitoring need a key. Firecrawl can also refuse anonymous requests from
some networks ("Anonymous keyless access is unavailable"); set a key if you see that.

### Exa

The `exa` server uses the same URL as Exa's official Claude Code plugin (`?client=claude-code-plugin`), which asks you
to **sign in once with OAuth**: run `/mcp`, pick `exa` and choose *Authenticate*. Claude Code stores the token, so no
key goes into any config. An Exa account (free tier available) also unlocks **Exa Agent** (the `exa-agent` skill). API
keys are managed at [dashboard.exa.ai/api-keys](https://dashboard.exa.ai/api-keys).

### Optional: Firecrawl CLI

The `firecrawl` and `firecrawl-search` skills prefer the Firecrawl CLI, which writes results to files (under
`.firecrawl/`, git-ignored) instead of the context window. They fall back to the MCP server when it isn't installed.

```bash
npm install -g firecrawl-cli
firecrawl login
```

## When to use which

| Task | Use | Why |
|---|---|---|
| Find pages by meaning ("startups doing X", "posts arguing Y") | **Exa** | Neural/semantic search, not keyword matching |
| Research a person, company or paper | **Exa** | Dedicated people, company and research-paper search |
| Deep, multi-step research with a cited write-up | **Exa** (`exa-search`, `exa-agent`) | Plans and runs many searches, reads sources |
| Get the content of a URL you already have | **Firecrawl** | Clean markdown from one page |
| Crawl a whole site or map its URLs | **Firecrawl** | `crawl` / `map` |
| JavaScript-rendered or dynamic pages | **Firecrawl** | Renders pages in a browser |
| Click, fill forms, log in, paginate | **Firecrawl** | Browser interaction |
| Watch a page for changes | **Firecrawl** | Change monitoring |
| Parse local files (PDF, DOCX, ...) | **Firecrawl** | `parse` |
| Quick keyword web search with page contents | Either | Firecrawl `search` with scraping, or Exa search |

A common combination: search with Exa, then scrape or crawl the best hits with Firecrawl.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Check server status | From a shell: `claude mcp list`. In Claude Code: `/mcp`. Both should show `firecrawl` and `exa` as connected. |
| Firecrawl says the API key is invalid, or `401 Unauthorized` | `FIRECRAWL_API_KEY` isn't set in the environment Claude Code started from. Export it and restart Claude Code from that shell. GUI-launched apps don't read `~/.zshrc`. |
| Firecrawl `429` or "rate limit" on the keyless tier | Set `FIRECRAWL_API_KEY`, or slow down. |
| Firecrawl crawl / map / interact refused | These need an API key; the keyless tier covers scrape, search and parse only. |
| `exa` shows *needs authentication* | Run `/mcp`, choose `exa`, and sign in with your Exa account. |
| Skills use MCP instead of the CLI | `firecrawl` isn't on the `PATH`, or isn't logged in. Run `firecrawl --version` and `firecrawl login`. |

## Layout

```
.claude-plugin/marketplace.json   single-plugin marketplace (firecrawl-exa, source "./")
.claude-plugin/plugin.json        plugin manifest, incl. mcpServers.firecrawl and mcpServers.exa (HTTP)
skills/firecrawl/                 Firecrawl CLI skill
skills/firecrawl-search/          Firecrawl search skill
skills/exa-agent/                 Exa Agent skill
skills/exa-search/                Exa deep-research skill
```

## Credits

- Firecrawl skills adapted from [firecrawl/cli](https://github.com/firecrawl/cli) (ISC), via
  [mppyxx/claude-power-pack](https://github.com/mppyxx/claude-power-pack).
- Exa skills and the Exa MCP entry from [exa-labs/exa-mcp-server](https://github.com/exa-labs/exa-mcp-server) (MIT),
  commit `15ffb50519e719dc791cdc750ce5ed1934c0a1ed`.

## License

Plugin code: MIT, see [LICENSE](LICENSE). Third-party skill files keep their own licences, see [NOTICE](NOTICE).
The hosted MCP servers belong to Firecrawl and Exa Labs and aren't part of this plugin.
