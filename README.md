# Keyfactor CLM Implementation

A design and operations documentation set for rolling out certificate lifecycle management (CLM) on Keyfactor Command,
with example automation scripts in PowerShell, Python, Go and Bash. It covers the target architecture, RBAC model,
certificate policies, enrollment methods, runbooks, monitoring, incident response, security controls, compliance
mapping, migration and cost, and shows how renewal, expiry monitoring, ServiceNow tickets and service reloads can be
automated against the Keyfactor Command REST API.

## Documentation

Start with [00-DOCUMENT-INDEX.md](00-DOCUMENT-INDEX.md) or the [executive summary](EXECUTIVE-SUMMARY.md).

| # | Document | Covers |
| --- | --- | --- |
| 01 | [Executive Design Document](01-Executive-Design-Document.md) | Scope, goals, target architecture |
| 02 | [RBAC Authorization Framework](02-RBAC-Authorization-Framework.md) | Roles, permissions and approval model |
| 03 | [Policy Catalog](03-Policy-Catalog.md) | Certificate templates, key and validity policies |
| 04 | [Architecture Diagrams](04-Architecture-Diagrams.md) | Component and flow diagrams |
| 05 | [Implementation Runbooks](05-Implementation-Runbooks.md) | Build steps by phase |
| 06 | [Automation Playbooks](06-Automation-Playbooks.md) | Renewal, deployment and reload workflows |
| 07 | [Enrollment Rails Guide](07-Enrollment-Rails-Guide.md) | ACME, EST, SCEP, API and auto-enrollment paths |
| 08 | [Operations Manual](08-Operations-Manual.md) | Day-to-day operation |
| 09 | [Monitoring and KPIs](09-Monitoring-KPIs.md) | Metrics, alerts, dashboards |
| 10 | [Incident Response Procedures](10-Incident-Response-Procedures.md) | Incident classification, common issues, emergency procedures, root cause analysis |
| 11 | [Security Controls](11-Security-Controls.md) | Hardening and key protection |
| 12 | [Compliance Mapping](12-Compliance-Mapping.md) | Mapping to SOC 2, PCI DSS, ISO 27001 and similar |
| 13 | [Threat Model](13-Threat-Model.md) | Threats and mitigations |
| 14 | [Integration Specifications](14-Integration-Specifications.md) | Azure Key Vault, HashiCorp Vault, cert-manager, AD CS, EJBCA and the Keyfactor API |
| 15 | [Testing and Validation](15-Testing-Validation.md) | Test plans |
| 16 | [Glossary and References](16-Glossary-References.md) | Terms and sources |
| 17 | [Architecture Decision Records](17-Architecture-Decision-Records.md) | Major decisions and their rationale |
| 18 | [Quick Start: First Sprint](18-Quick-Start-First-Sprint.md) | A two-week starting plan |
| 19 | [Service Owner Guide](19-Service-Owner-Guide.md) | How application teams request and renew certificates |
| 20 | [Vendor Evaluation Criteria](20-Vendor-Evaluation-Criteria.md) | Scoring framework for CLM vendors |
| 21 | [Migration Strategy](21-Migration-Strategy.md) | Phased migration from existing PKI |
| 22 | [Cost Analysis](22-Cost-Analysis.md) | Five-year cost model |

Also: [Keyfactor integrations guide](KEYFACTOR-INTEGRATIONS-GUIDE.md), [asset inventory
integration](ASSET-INVENTORY-INTEGRATION-GUIDE.md) with
[`asset-inventory-template.csv`](asset-inventory-template.csv).

## Automation scripts

Example implementations that call the Keyfactor Command REST API (`/KeyfactorAPI/Certificates`) and related services.
Most exist in more than one language so you can pick the one that fits your platform. See
[automation/README.md](automation/README.md) and [automation/SCRIPT-MATRIX.md](automation/SCRIPT-MATRIX.md).

| Task | Scripts |
| --- | --- |
| Expiry monitoring and alerts | `automation/monitoring/monitor-expiry.{ps1,py,go}` |
| Automatic renewal | `automation/renewal/auto-renew.{ps1,py,go}`, `renew-with-approval.ps1` |
| Inventory report | `automation/reporting/generate-inventory-report.{ps1,py,go}` |
| Keyfactor webhooks | `automation/webhooks/webhook-receiver.{py,go}`, `azure-function-webhook.ps1` |
| ServiceNow tickets | `automation/itsm/servicenow-integration.{ps1,py}` |
| Service reload after renewal | `automation/service-reload/reload-iis.ps1`, `reload-nginx.sh` |
| Certificate deployment pipeline | `automation/deployment/azure-pipelines-cert-deploy.yml` |
| Keyfactor database backup | `automation/backup/backup-keyfactor-db.ps1` |
| Device validation before enrollment | `scripts/validate-device.{ps1,py,go,sh}`: checks a hostname against a CSV inventory, an asset database, Azure, Kubernetes or ServiceNow ([scripts/README.md](scripts/README.md)) |

Settings come from environment variables, for example:

```bash
export KEYFACTOR_HOST=keyfactor.contoso.com
export KEYFACTOR_DOMAIN=CONTOSO
export KEYFACTOR_USERNAME=svc-keyfactor-api
export KEYFACTOR_PASSWORD='...'
python3 automation/monitoring/monitor-expiry.py
```

Other variables used by some scripts: `SERVICENOW_INSTANCE`, `SERVICENOW_USER`, `SERVICENOW_PASSWORD`,
`SLACK_WEBHOOK_URL`, `ALERT_WEBHOOK_URL`, `WEBHOOK_SECRET`, `ASSET_DB_HOST`, `ASSET_DB_PASSWORD`,
`RENEWAL_THRESHOLD_DAYS`, `WARNING_DAYS`, `CRITICAL_DAYS`, `DRY_RUN`.

Requirements by language: PowerShell 7 for `validate-device.ps1` (5.1 or later for the rest); Python 3 with `requests`
(plus `flask` for the webhook receiver and `pandas` for the inventory report); Go, standard library only; Bash with
`curl` (and `jq` for `validate-device.sh`).

## Status

- The documents describe a target design and plan. They are not tied to a specific production deployment, and figures
  such as costs and timelines are planning estimates to adapt.
- The scripts are reference implementations. They pass syntax checks (PowerShell parser, Python compile and pyflakes,
  `go build` and `go vet`, shellcheck) but have not been run against a live Keyfactor Command instance in this
  repository. Check the API endpoints and authentication against your Keyfactor version before use, and run
  `auto-renew.py` with `DRY_RUN=true` first.

## License

MIT. See [LICENSE](LICENSE).

## Author

Adrian Johnson · [github.com/adrian207](https://github.com/adrian207)
