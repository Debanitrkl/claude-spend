# Coding Agent Usage

Token usage dashboard for **Claude Code**, **Gemini CLI**, and **Codex CLI** — with built-in OpenTelemetry export.

See where your tokens go across all your AI coding tools. Optionally send the metrics to any OTLP-compatible backend (Grafana, Datadog, Parseable, Prometheus, etc.).

> Fork of [claude-spend](https://github.com/writetoaniketparihar-collab/claude-spend) by [Aniket Parihar](https://github.com/writetoaniketparihar-collab) — thank you for building the original. This fork adds multi-tool support, a redesigned UI, and OTLP export.

## Install

```
npx coding-agent-usage
```

That's it. Opens a dashboard in your browser.

## What it does

- Reads local session files from **Claude Code**, **Gemini CLI**, and **Codex CLI** (nothing leaves your machine)
- Shows token usage per conversation, per day, and per model across all tools
- Filter sessions by provider (Claude / Gemini / Codex)
- Dark and light theme with persistent preference
- Surfaces insights like which prompts cost the most, usage patterns, and multi-tool stats
- **Exports token usage metrics to any OTLP endpoint** (opt-in)

## Supported tools

| Tool | Session location | Format |
|---|---|---|
| Claude Code | `~/.claude/projects/` | JSONL |
| Gemini CLI | `~/.gemini/tmp/*/chats/` | JSON |
| Codex CLI | `~/.codex/sessions/` | JSONL |

Each tool is parsed independently. If a tool isn't installed or has no sessions, it's silently skipped.

## Screenshots

![Dashboard overview — stats, insights, and provider breakdown](screenshots/dashboard-overview.png)

![Daily token usage chart and model breakdown donut](screenshots/charts.png)

![Sessions table with provider filter chips](screenshots/sessions.png)

## Options

```
coding-agent-usage --port 8080       # custom port (default: 3456)
coding-agent-usage --no-open         # don't auto-open browser
```

## OTLP Export

Send your token usage metrics to any OpenTelemetry-compatible backend.

### From the dashboard (no restart needed)

The **OpenTelemetry Export** card lets you configure the endpoint and headers directly from the UI. It tests connectivity before connecting, shows export status, and supports disconnect/reconnect without restarting.

### From the CLI

```bash
# Basic
coding-agent-usage --otlp-endpoint http://localhost:4318

# With authentication
coding-agent-usage --otlp-endpoint https://otel.example.com \
  --otlp-headers "Authorization: Basic dXNlcjpwYXNz"

# Multiple headers (can be specified multiple times)
coding-agent-usage --otlp-endpoint https://otel.example.com \
  --otlp-headers "Authorization: Basic dXNlcjpwYXNz" \
  --otlp-headers "X-P-Stream: my-stream"
```

Headers support both `Key: Value` and `Key=Value` formats, and handle base64 values correctly.

### Environment variables

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
export OTEL_EXPORTER_OTLP_HEADERS="Authorization: Basic dXNlcjpwYXNz, X-Custom: value"
coding-agent-usage
```

### Metrics exported

12 metric instruments covering all the token usage data:

| Metric | Type | Attributes | Description |
|---|---|---|---|
| `agent.usage.tokens.input` | Counter | model, project, date | Input tokens consumed |
| `agent.usage.tokens.output` | Counter | model, project, date | Output tokens generated |
| `agent.usage.queries` | Counter | model, project, date | Query round-trips |
| `agent.usage.sessions` | Counter | project | Session count |
| `agent.usage.session.tokens` | Histogram | model, project, date | Token distribution per session |
| `agent.usage.session.queries` | Histogram | model, project, date | Query count distribution |
| `agent.usage.daily.tokens.input` | Gauge | date | Daily input tokens |
| `agent.usage.daily.tokens.output` | Gauge | date | Daily output tokens |
| `agent.usage.daily.sessions` | Gauge | date | Daily session count |
| `agent.usage.model.tokens.input` | Gauge | model | Per-model input tokens |
| `agent.usage.model.tokens.output` | Gauge | model | Per-model output tokens |
| `agent.usage.model.queries` | Gauge | model | Per-model query count |

### Continuous export

When connected, session files are re-parsed and metrics are re-exported **every 60 seconds** — so your observability backend always has fresh data as new sessions happen in the background.

### Endpoint validation

Before connecting, the endpoint is tested with a ping. You'll get specific error messages if something is wrong:

- `Connection refused — no service listening at ...`
- `DNS lookup failed — hostname not found for ...`
- `Connection timed out after 8s`
- HTTP errors (401 Unauthorized, 403 Forbidden, etc.)

## Privacy

All data stays local. Reads files from `~/.claude/`, `~/.gemini/`, and `~/.codex/` on your machine and serves a dashboard on localhost. No data is sent anywhere unless you explicitly configure an OTLP endpoint.

## Credits

Built on top of [claude-spend](https://github.com/writetoaniketparihar-collab/claude-spend) by [Aniket Parihar](https://www.linkedin.com/in/aniketparihar/). Multi-tool support, UI redesign, and OTLP export by [Debabrata Panigrahi](https://www.linkedin.com/in/debanitr/).

## License

MIT
