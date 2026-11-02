# VRL Disaster Recovery Master Plan

**Purpose:** If the original Supabase project is permanently lost, rebuild VRL on a replacement Supabase project and recover existing users' VRL IDs, passwords (password hashes; never plaintext), and virtual-point balances.

## Recovery guarantee (only after tested)
- Restore PostgreSQL schema, migrations, tables, functions/RPCs, triggers, policies, and data.
- Restore Supabase Auth users with original UUIDs and password hashes so existing passwords remain valid.
- Restore wallets and ledger/account records consistently (do not restore balances alone).
- Redeploy Edge Functions and restore required Auth/provider settings and secrets from a separately secured secret inventory.
- Redeploy player/admin frontends from GitHub and update Supabase project URL/keys and hosting environment.
- Validate admin login, player login with an existing password, wallet balance, hierarchy, round flow, transfers, and settlement before reopening.

## Current known environment
- Production/testing project currently audited: VRL-Testing, project ref `yjdkjidcjafshwsaiado` (Free plan; no Supabase-managed daily backups/PITR shown in dashboard).
- VRL-Development project ref `gsfdzwhryqwlxijdgzco`: never target for this recovery/audit.
- Source repositories:
  - `potla270-ops/vrl-game`
  - `potla270-ops/vrl-admin-portal`
- Live player site: `https://vrl22.in`; admin: `https://admin.vrl22.in/admin.html`

## Critical security rules
- Never commit database dumps, Auth exports, password hashes, service-role keys, JWT secrets, or environment secrets to GitHub.
- Store backup archives encrypted, outside the original Supabase project and separately from GitHub source.
- Keep encryption key/recovery passphrase in a separate secure location; losing it makes backups unusable.
- Never overwrite VRL-Development or restore onto VRL-Testing during a recovery drill.
- Never invent/reconstruct wallet balances from memory; restore authoritative data and reconcile ledger.
- Preserve Auth UUIDs and password hashes; CSV of VRL IDs/balances is not sufficient for existing-password continuity.

## Backup system status (2026-11-03)
- Supabase Free dashboard: no scheduled database backup history; PITR not enabled.
- Independent encrypted automated backups: NOT IMPLEMENTED / NOT VERIFIED.
- Auth-preserving restore rehearsal: NOT TESTED.
- Secret/config inventory: NOT COMPLETED.
- Recovery time target: 30–60 minutes is aspirational, NOT measured or guaranteed.

## Implementation checklist
1. Pin a clean schema/migration baseline and record deployed commit SHAs.
2. Set up a trusted backup runner outside Supabase; use least-privilege credentials where feasible.
3. Produce encrypted full PostgreSQL backups on a schedule (initial target hourly; confirm storage/cost/operational safety).
4. Ensure Auth data including password hashes is captured securely and verify the supported restore route into a NEW project.
5. Back up Edge Function source, deployment configuration, Auth settings, frontend environment inventory, and hosting/domain instructions (secrets encrypted separately).
6. Keep backup generations and verify checksums; alert on failed/missing backup.
7. Create a temporary isolated Supabase recovery project and perform a restore rehearsal.
8. Test existing VRL ID/password login, Auth UUID continuity, wallet/ledger reconciliation, hierarchy, admin/player paths, round/settlement, and transfer.
9. Document exact restore commands and measure recovery time.
10. Repeat restore test periodically and after schema/auth changes.

## Incident instruction
If user says “VRL project is permanently lost,” first locate the latest verified encrypted backup and its key, confirm the replacement project ref, then follow this plan. Do not claim recovery until restore and login/balance tests pass.

**This file is a recovery plan, not a backup archive and not proof that recovery is currently possible.**
