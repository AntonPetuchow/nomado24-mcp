# Nomado24 Remote Jobs MCP Server

Public remote MCP (Model Context Protocol) server for the [Nomado24](https://www.nomado24.de) job index: remote and hybrid roles for Germany and the EU, in German, English and French, plus aggregate market statistics.

Read-only, free, no authentication, no signup. Attribution is required.

## Endpoint

```
https://api.nomado24.de/api/public/v1/mcp
```

- Transport: Streamable HTTP, POST only, stateless
- Auth: none
- Rate limit: 120 requests per 15 minutes per IP
- Attribution: required. Any use of the data must credit nomado24.de with a link.

## Connect

Claude Desktop, in `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "nomado24": {
      "type": "http",
      "url": "https://api.nomado24.de/api/public/v1/mcp"
    }
  }
}
```

Any client that speaks Streamable HTTP needs nothing but the URL. There is no session id to manage: every POST is answered on its own, so a client that keeps no state behaves exactly like one that does.

## Tools

All three are annotated `readOnlyHint: true`, `destructiveHint: false`, `idempotentHint: true`, `openWorldHint: false`.

### `search_jobs`

Full-text search over the index.

| Parameter | Type | Notes |
|---|---|---|
| `q` | string, max 200 chars | Free text over job title, company and tags |
| `language` | `de`, `en` or `fr` | Filters by the language the posting is written in |
| `page` | integer, 1 to 1000 | Default 1 |
| `per_page` | integer, 1 to 25 | Default 20 |

```bash
curl -X POST https://api.nomado24.de/api/public/v1/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"search_jobs","arguments":{"q":"python","language":"en","per_page":2}}}'
```

Abridged `structuredContent`:

```json
{
  "jobs": [
    {
      "slug": "people-analytics-manager-mfx-4bf7ad7c35",
      "title": "People Analytics Manager (m/f/x)",
      "companyName": "Scalable GmbH",
      "location": "Germany only",
      "remote": false,
      "workArrangement": "hybrid",
      "language": "en",
      "tags": ["smartrecruiters", "People", "Full-time", "Mid-Senior Level"],
      "source": "smartrecruiters",
      "publishedAt": "2026-09-08T15:08:28.943Z",
      "url": "https://www.nomado24.de/en/remote-jobs/job/people-analytics-manager-mfx-4bf7ad7c35"
    }
  ],
  "page": 1,
  "perPage": 2,
  "count": 2,
  "attribution": {
    "text": "Source: nomado24.de, free to use with an attribution link to https://www.nomado24.de",
    "docsUrl": "https://www.nomado24.de/en/developers"
  }
}
```

### `get_job`

Fetches one posting by id, with the full payload: the posting body where the source permits redistribution, plus per-field provenance, meaning who asserted each field, by which method, and when.

| Parameter | Type | Notes |
|---|---|---|
| `id` | string, required | The id returned by `search_jobs` (`job_<slug>`). A bare slug is accepted too |

```bash
curl -X POST https://api.nomado24.de/api/public/v1/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"get_job","arguments":{"id":"job_people-analytics-manager-mfx-4bf7ad7c35"}}}'
```

Not every posting carries a full body. Some sources permit only an excerpt to be redistributed. Those responses are marked as truncated and point back to the nomado24 posting page.

### `get_job_statistics`

Aggregate statistics for the whole index. Takes no arguments.

```bash
curl -X POST https://api.nomado24.de/api/public/v1/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"get_job_statistics","arguments":{}}}'
```

Returns totals, plus breakdowns by source, work arrangement, detected language and location scope, and salary data points.

## What is in the index

The numbers below are a snapshot from 8 September 2026. Call `get_job_statistics` for live figures rather than trusting this section.

| | |
|---|---|
| Active postings | 13,505 |
| Hybrid | 7,041 |
| Fully remote | 6,464 |
| Companies | 2,679 |
| Languages | English 6,976, German 6,010, French 519 |

Two things worth knowing before you build on this:

- **The index is not remote only.** Roughly half of it is hybrid. If your use case needs fully remote roles, filter on `workArrangement`, and do not quote the total as a remote-job count.
- **The geographic focus is Germany and the EU.** Postings outside that scope exist, but they are the exception.

## Attribution

Use of the data is free and requires a credit with a link to nomado24.de. Every tool response carries the exact wording in `structuredContent.attribution`.

## More

- Human docs: https://www.nomado24.de/en/developers
- REST API over the same index, with an OpenAPI spec: https://www.nomado24.de/openapi.yaml
- Open dataset (CC BY 4.0): https://www.nomado24.de/remote-jobs-statistik.json
- Operator: Nomado24 UG (haftungsbeschränkt), Ludwigshafen am Rhein, Germany
- Contact: anton.petuchow@nomado24.de

The `server.json` in this repo mirrors the entry published to the official MCP Registry.
