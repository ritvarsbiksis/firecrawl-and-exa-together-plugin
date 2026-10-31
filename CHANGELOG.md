# Changelog

## 0.1.0 — 2026-10-29

- Initial release.
- MCP server `firecrawl`: Firecrawl's hosted server (`https://mcp.firecrawl.dev/v2/mcp`, HTTP), authenticated with
  `Authorization: Bearer ${FIRECRAWL_API_KEY}` from the environment. Same entry `firecrawl setup mcp` writes for Claude Code.
- MCP server `exa`: Exa's hosted server (`https://mcp.exa.ai/mcp?client=claude-code-plugin`, HTTP), as in the official
  Exa plugin (exa-labs/exa-mcp-server @ 15ffb50).
- Skills `firecrawl` and `firecrawl-search`, adapted from firecrawl/cli (ISC). They prefer the Firecrawl CLI and fall
  back to the MCP server.
- Skills `exa-agent` and `exa-search` (upstream `search`, renamed), from exa-labs/exa-mcp-server (MIT).
- README with install, configuration, a when-to-use-which routing table and troubleshooting. Third-party licences in NOTICE.
