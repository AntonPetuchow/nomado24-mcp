# Nomado24 Remote Jobs MCP Server

Public remote MCP (Model Context Protocol) server for the [Nomado24](https://www.nomado24.de) remote-job index: nearly 10,000 curated remote and hybrid jobs for Germany and the EU, plus aggregate market statistics.

## Endpoint

```
https://api.nomado24.de/api/public/v1/mcp
```

- Transport: Streamable HTTP (POST only, stateless)
- Auth: none
- Cost: free
- Attribution: required. Any use of the data must credit nomado24.de with a link.

## Tools

| Tool | Description |
|---|---|
| `search_jobs` | Full-text search over the remote/hybrid job index. Filters: free-text `q`, `language` (de/en/fr), pagination (max 25 per page). |
| `get_job_statistics` | Aggregate statistics of the index: active jobs, new in last 7/30 days, companies, top source, salary data points. |

## Example (initialize)

```bash
curl -X POST https://api.nomado24.de/api/public/v1/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-03-26","capabilities":{},"clientInfo":{"name":"example","version":"1.0"}}}'
```

## More

- Human docs: https://www.nomado24.de/en/developers
- REST API (same index): see docs above; OpenAPI at https://www.nomado24.de/openapi.yaml
- Open dataset (CC BY 4.0): https://www.nomado24.de/remote-jobs-statistik.json
- Operator: Nomado24 UG (haftungsbeschränkt), Ludwigshafen am Rhein, Germany
- Contact: anton.petuchow@nomado24.de

The `server.json` in this repo mirrors the entry published to the official MCP Registry.
