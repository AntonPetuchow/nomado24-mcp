# Nomado24 Remote Jobs MCP Server

Public remote MCP (Model Context Protocol) server for the [Nomado24](https://www.nomado24.de) remote-job index: more than 10,000 curated remote and hybrid jobs for Germany and the EU (live count on https://www.nomado24.de/en/remote-jobs/statistik), plus aggregate market statistics.

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
| `search_jobs` | Search over the remote/hybrid job index. Two contracts: free-text `q`, `language` (de/en/fr) and page-based paging (max 25 per page) return the legacy v1 shape; any structured filter (`country`, `applicant_region`, `work_arrangement`, `employment_type`, `seniority`, `skills`, `salary_min`/`salary_max`, `published_after`, `verified_after`, `source`, `company`, `sort`) or a `cursor` returns the v2 contract with per-posting provenance and keyset paging. |
| `get_job` | One posting by id (`job_<slug>` or a bare slug) with the complete v2 payload: posting body where the source permits redistribution, per-field provenance, salary origin, first-seen / last-verified dates. Expired postings are not served. |
| `get_job_statistics` | Aggregate statistics of the index: active jobs, new in last 7/30 days, companies, top source, salary data points. |

The full parameter schema of every tool, with bounds and enums, is published at https://www.nomado24.de/.well-known/mcp/server.json and answered live by `tools/list`.

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
