# Sicherheitsrichtlinien für dieses Repository

## Zugangsdaten: AWS und Anthropic

- Keine Zugangsdaten im Repository, weder in Code, Konfiguration, Tests, Fixtures, Beispielen, Dokumentation noch Skripten. Insbesondere:
  - AWS Access Key IDs (`AKIA…`, `ASIA…`), Secret Access Keys und Session Tokens
  - Anthropic API-Schlüssel (`sk-ant-…`)
  - Workspace-API-Schlüssel von Claude Platform on AWS
  - Tokens für LLM-Gateways oder Proxys
- Die Variablen `ANTHROPIC_AWS_API_KEY`, `ANTHROPIC_API_KEY`, `ANTHROPIC_AUTH_TOKEN`, `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` und `AWS_SESSION_TOKEN` werden nie mit einem festen Wert belegt. Das gilt für Code (`process.env.X = "…"`, `os.environ["X"] = "…"`), eingecheckte `.env`-Dateien, den `env`-Block einer eingecheckten `.claude/settings.json`, Dockerfiles, CI-Workflows und Shell-Skripte. Werte kommen nur aus der Laufzeitumgebung, einem Secret-Manager (z. B. AWS Secrets Manager, GitHub Actions Secrets) oder der persönlichen `~/.claude/settings.json`.
- Nicht geheime Einstellungen wie `CLAUDE_CODE_USE_ANTHROPIC_AWS`, `ANTHROPIC_AWS_WORKSPACE_ID` und `AWS_REGION` dürfen eingecheckt werden.
- AWS-Zugriff bevorzugt über SigV4 mit kurzlebigen Anmeldedaten: AWS SSO lokal, IAM-Rolle auf Servern, OIDC-Rolle in CI. Code oder CI-Konfiguration, die neu langlebige Access Keys oder Workspace-API-Schlüssel einführt, als Finding melden und auf eine Rolle verweisen.
- Zugangsdaten nie loggen oder ausgeben, auch nicht teilweise: nicht in Logs, Fehlermeldungen, Exceptions, Telemetrie oder Antworten an Clients. Beim Protokollieren von Requests die Header `x-api-key` und `Authorization` schwärzen.
- `ANTHROPIC_AWS_BASE_URL` muss eine `https://`-URL sein.
- `CLAUDE_CODE_SKIP_ANTHROPIC_AWS_AUTH=1` nur zusammen mit einer `ANTHROPIC_AWS_BASE_URL`, die auf ein eigenes Gateway zeigt, das die Anfragen selbst signiert.
- `.env` und `.env.*` (außer einer `.env.example` mit leeren Platzhaltern), `.claude/settings.local.json`, `.claude/claude-security-guidance.local.md` sowie Kopien von `~/.aws/credentials` gehören in die `.gitignore`.
