# which-tenant-am-i-on — Connector Identity Probe Reference

**Version:** 1.0  
**Status:** [VALIDATED]  
**Implements:** ADR-002 (Tenant Verification Probe for Cold-Start Interviews)  
**Referenced by:** all `cold-start-interview` skills; any skill that encounters `⚠️ wrong tenant` in config

---

## Purpose

This reference defines the identity probe call for each connector type used in cold-start interviews. When a connector call succeeds, run the probe for that connector type to verify *which tenant* the connector is authenticated to — not just that it responds.

A connector can be configured, authenticated, and MCP-responding against the wrong organization (e.g., a prior employer's CRM). The probe catches this before it silently poisons all downstream skill output.

---

## Probe Table

| Connector type | Identity probe call | Display to user |
|----------------|---------------------|-----------------|
| CRM — HubSpot | List 1 company or contact with name field | `CRM tenant: [Company name from first result]. Is this your company's CRM?` |
| CRM — Salesforce | Query Account object, return Name of first result | `CRM tenant: [Account name]. Is this your company's CRM?` |
| CS Platform — Gainsight | Read authenticated user profile or company info | `CS Platform tenant: [company or user org]. Correct?` |
| CS Platform — Totango | Read authenticated organization name | `CS Platform tenant: [org name]. Correct?` |
| CS Platform — ChurnZero | Read authenticated account or company name | `CS Platform tenant: [company name]. Correct?` |
| Ticketing — Zendesk | Read organization name from `/api/v2/organizations.json` (first result) | `Ticketing tenant: [org name]. Correct?` |
| Ticketing — Jira | Read `serverInfo` or `myself` endpoint, surface `displayName` and `serverTitle` | `Ticketing tenant: [server/org name]. Correct?` |
| Calendar/Email — Google Workspace | Read authenticated user email via `userinfo` endpoint | `Calendar/Email: Connected as [user@domain.com]. Correct?` |
| Calendar/Email — Microsoft 365 | Read authenticated user UPN from `/me` endpoint | `Calendar/Email: Connected as [user@domain.com]. Correct?` |
| Generic / unknown | Run any available read call; surface `type: [result type], value: [first identifiable field]` | `Connector returned: [summary]. Does this look like your data?` |

**Data minimization rule:** The probe reads only the minimum identity-confirming field (org name, account name, email domain). It does NOT read PII, deal amounts, health scores, or any sensitive record content.

---

## Probe Result States

After running the probe, write one of four states to the integrations table:

| State | When | Config tag |
|-------|------|-----------|
| `✓ verified` | Call succeeded AND user confirmed "yes, correct tenant" | `✓ verified ([tenant identifier])` |
| `✓ connected (tenant unverified)` | Call succeeded but probe endpoint unavailable or timed out | `✓ connected (tenant unverified — probe call failed)` |
| `⚠️ wrong tenant` | Call succeeded AND user said "no, wrong tenant" | `⚠️ wrong tenant — [observed tenant] (re-authenticate before use)` |
| `⚪ configured, not tested` | Connector configured but MCP call not attempted this session | `⚪ configured, not tested` |

---

## Integrations Table Format

Replace the binary ✓ / ⚪ format with the expanded 3-column table:

```
| Connector | Status | Tenant |
|-----------|--------|--------|
| HubSpot CRM | ✓ verified | Acme Corp |
| Gainsight | ✓ connected (tenant unverified) | — |
| Google Calendar | ⚠️ wrong tenant | previous-employer.com (re-authenticate) |
| Jira | ⚪ configured, not tested | — |
```

---

## User-Facing Presentation

Present after each connector confirms ✓ (call succeeded):

> **Tenant check — [connector name]**  
> `[identity probe result]`  
> Is this the right [company / org / account]?
>
> **Yes** → proceed, write `✓ verified ([tenant identifier])` to config  
> **No** → mark `⚠️ wrong tenant — re-authenticate before use`, surface reauthentication instructions, continue interview

**Wrong-tenant handling:** Do NOT block the rest of the interview when a connector is flagged ⚠️. Continue with remaining connectors. At interview close, surface a summary of all flagged connectors:

> **Connectors requiring re-authentication before use:**
> - [Connector name] — connected as [wrong tenant]. Re-authenticate at [auth URL or instructions].

---

## Relationship to `[configured but unverified]` Tag (D-10)

These are distinct confidence states — do not conflate them:

| Tag | What it means |
|-----|---------------|
| `[configured but unverified]` (D-10) | A *config field value* was written from user input without CRM/platform verification — the data point itself is unconfirmed |
| `✓ connected (tenant unverified)` (D-9) | The *connector* responded but the identity probe for tenant confirmation failed or was skipped — the connection exists, the tenant is unknown |

Both tags may appear simultaneously on the same connector entry.

---

## Skills That Reference This File

- `csm/skills/cold-start-interview/SKILL.md`
- `cs-ops/skills/cold-start-interview/SKILL.md`
- `renewals/skills/cold-start-interview/SKILL.md`
- `onboarding/skills/cold-start-interview/SKILL.md`
- `rev-ops/skills/cold-start-interview/SKILL.md`

Any skill that reads `⚠️ wrong tenant` from config and needs to surface a remediation path should reference this file for the connector-type-specific re-authentication guidance.

---

## Open Questions (from ADR-002)

1. Should the tenant probe run silently on every skill execution (not just cold-start) and warn when live tenant doesn't match what was stored at setup? Would catch mid-lifecycle credential rotation to a different tenant.
2. For connectors with no suitable identity endpoint, should the probe ask the user to manually confirm the tenant name, or log `unverified` automatically?
3. The 30-day stale threshold for `--check-integrations` re-probe is [Low Confidence — pending product team confirmation].
