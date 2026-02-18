# claude-spend

See where your Claude Code tokens go. One command, zero setup.

## Problem

 I've been using Claude Code every day for 3 months. I hit the usage limit almost daily, but had zero visibility into which prompts were eating my tokens. So I built claude-spend. One command, zero setup. 

## How does it look


<img width="1910" height="966" alt="Screenshot 2026-02-18 092727" src="https://github.com/user-attachments/assets/11cc7149-d4dd-4e44-a3a0-0b48e935b7bc" />

<img width="1906" height="966" alt="Screenshot 2026-02-18 093529" src="https://github.com/user-attachments/assets/537c3611-5794-41d2-864e-e368e6949812" />

<img width="1908" height="969" alt="Screenshot 2026-02-18 093647" src="https://github.com/user-attachments/assets/aaaa8ce5-2025-407d-8596-ea1965748691" />

<img width="1908" height="969" alt="Screenshot 2026-02-18 093647" src="https://github.com/user-attachments/assets/a9fde5e2-6e52-4bae-9b96-03655109aef6" />



## Install

```
npx claude-spend
```

That's it. Opens a dashboard in your browser.


## What it does

- Reads your local Claude Code session files (nothing leaves your machine)
- Shows token usage per conversation, per day, and per model
- Surfaces insights like which prompts cost the most and usage patterns


## Options

```
claude-spend --port 8080   # custom port (default: 3456)
claude-spend --no-open     # don't auto-open browser
```

## OTLP Export

Send your token usage metrics to any OpenTelemetry-compatible backend (Grafana, Datadog, Parseable, Prometheus, etc.).

### From the dashboard

Click the **OpenTelemetry Export** card in the dashboard to configure the endpoint and headers — no restart needed.

### From the CLI

```bash
claude-spend --otlp-endpoint http://localhost:4318
```

With authentication:

```bash
claude-spend --otlp-endpoint https://otel.example.com \
  --otlp-headers "Authorization: Basic dXNlcjpwYXNz"
```

Headers can be specified multiple times and support both `Key: Value` and `Key=Value` formats.

### Environment variables

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
export OTEL_EXPORTER_OTLP_HEADERS="Authorization: Basic dXNlcjpwYXNz"
claude-spend
```

### Metrics exported

| Metric | Type | Attributes |
|---|---|---|
| `claude.spend.tokens.input` | Counter | model, project, date |
| `claude.spend.tokens.output` | Counter | model, project, date |
| `claude.spend.queries` | Counter | model, project, date |
| `claude.spend.sessions` | Counter | project |
| `claude.spend.session.tokens` | Histogram | model, project, date |
| `claude.spend.session.queries` | Histogram | model, project, date |
| `claude.spend.daily.tokens.input` | Gauge | date |
| `claude.spend.daily.tokens.output` | Gauge | date |
| `claude.spend.daily.sessions` | Gauge | date |
| `claude.spend.model.tokens.input` | Gauge | model |
| `claude.spend.model.tokens.output` | Gauge | model |
| `claude.spend.model.queries` | Gauge | model |

When connected, metrics are re-parsed and exported every 60 seconds.

## Privacy

All data stays local. claude-spend reads files from `~/.claude/` on your machine and serves a dashboard on localhost. No data is sent anywhere unless you configure an OTLP endpoint.

## License

MIT
