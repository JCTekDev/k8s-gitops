# Liqui — Liquibase Support Engineer (OpenClaw)

AI assistant specialised in Liquibase migrations, changelogs, and database schema versioning, deployed via the [OpenClaw](https://serhanekicii.github.io/openclaw-helm) helm chart.

- **Namespace:** `liquibot-prod`
- **ArgoCD project:** `prod`
- **Chart:** `openclaw` `1.5.3` — `https://serhanekicii.github.io/openclaw-helm`
- **Channel:** Slack
- **Embedded browser:** Disabled (`browser.enabled: false`) to avoid headless-shell container runtime errors

---

## Setup

### 1. Prepare secrets

Slack app creation, credential collection, and sealed-secret generation are documented in:

`k8s-manifests-private/liquibot/README.md`

### 3. Deploy via ArgoCD

Push the contents of `k8s-gitops` to trigger the root-app sync, or sync manually:

```bash
argocd app sync liquibot-secrets
argocd app sync liquibot
```

ArgoCD will apply in sync-wave order:
1. **wave 0** — `liquibot-secrets` creates the `SealedSecret` in `liquibot-prod`
2. **wave 1** — `liquibot` deploys the OpenClaw helm chart

### 4. Pair a Slack user with the gateway

OpenClaw connects to Slack via **Socket Mode**: the pod establishes an outbound WebSocket to Slack's servers using the `SLACK_APP_TOKEN`. No inbound port or Ingress is needed for Slack to reach the agent.

The port-forward below is only needed **once**, to reach the OpenClaw gateway HTTP API from your local machine in order to run `/pair`. After pairing you can drop it.

```bash
kubectl -n liquibot-prod port-forward svc/liquibot 18789:18789
```

Then in Slack, send a direct message to the bot:

```
/pair <OPENCLAW_GATEWAY_TOKEN>
```

Once paired, communication between Slack and the agent flows entirely through the Socket Mode WebSocket inside the pod.

---

## Updating the chart version

Edit `targetRevision` in `liquibot-app.yaml` and push.
Latest releases: https://github.com/serhanekicii/openclaw-helm/releases

## Troubleshooting

If you see errors from `headless-shell` (OOM score / shared memory descriptor lookup), keep the embedded browser disabled in `liquibot-app.yaml`. Re-enable it only if your agent explicitly needs browser automation tools.
