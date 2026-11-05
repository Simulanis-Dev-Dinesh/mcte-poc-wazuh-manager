# mcte-poc-wazuh - CyberRangeCZ sandbox definition

Three-node Wazuh stack for the MCTE Mhow POC, built on images fixed for OpenStack.

| Host | Image | Flavor | IP |
|---|---|---|---|
| wazuh-manager | `wazuh-manager-ci` | `wazuh.manager` (2 vCPU / 4 GB / 40 GB) | 10.10.20.10 |
| wazuh-indexer | `wazuh-indexer-ci` | `wazuh.indexer` (2 vCPU / 8 GB / 60 GB) | 10.10.20.11 |
| wazuh-dashboard | `wazuh-dashboard-ci` | `wazuh.dashboard` (1 vCPU / 2 GB / 40 GB) | 10.10.20.12 |
| edge | `debian-12-x86_64` | `standard.small` | 10.10.20.1 |

Management user on every node: `crczp` (created inside the images, passwordless sudo).

## Files

- `topology.yml` - the three-node definition the platform reads.
- `topology-manager-only.yml` - single manager host, kept for the quick test.
- `provisioning/playbook.yml` - four plays: Common, manager, indexer, dashboard.
  Hosts are selected by inventory name, so the same playbook serves the
  manager-only and the three-node topology.
- `provisioning/templates/` - `ossec.conf`, `opensearch.yml`,
  `opensearch_dashboards.yml`, `wazuh.yml`.

## POC switches

Certificates are not present in the images yet, so the stack runs without TLS:

- `wazuh_indexer_security: "no"` - indexer with `plugins.security.disabled: true`
- `wazuh_dashboard_indexer_security: "no"` - dashboard talks plain HTTP to the indexer
- `wazuh_force_ossec_conf` / `wazuh_force_dashboard_conf` / `wazuh_force_indexer_conf`
  are `"yes"`, so a fresh config is written over the wiped one inside the images

Set the security switches to `"yes"` on both sides once certificates exist.

## Offline-safe

No apt, no package installs, no outbound network in the provisioning run.
