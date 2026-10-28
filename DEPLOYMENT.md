# Production deployment

- Site: https://monitor.606677.xyz
- Worker: `cf-server-monitor`
- D1: `cf-server-monitor-db`
- Deployment branch: `deployment/monitor`
- Official upstream: `huilang-me/CF-Server-Monitor`, branch `main`

The `Monitor automatic upgrade` GitHub Actions workflow runs daily at 07:30 Asia/Shanghai and can also be dispatched manually. It merges upstream without force, retains the local workflow definitions, runs the full tests and build, verifies a D1 Time Travel recovery point, deploys with `--keep-vars`, checks the two reporting agents, and retains the validated merge. Merge conflicts, high-severity dependency advisories, failed tests, missing recovery points or failed health checks stop the run.

`CF_API_TOKEN` and `CF_ACCOUNT_ID` are GitHub Actions Secrets. Agent keys and administrator passwords are not stored in GitHub. The existing Worker secret and D1 settings are preserved during upgrades. D1 recovery-point artifacts contain bookmarks only, not a database export.

VMR and VMI independently run the official `cf-probe` updater as the non-root `cfsm` user. Its built-in update check runs every six hours, independently of GitHub Actions and the local Mac. Configuration is in `/var/lib/cfsm/.cf-probe/config.conf`, permission 0600.

The three upstream deployment/sync workflows are disabled for this fork to prevent duplicate production deployments. Do not modify the unrelated automation-3 inventory, proxy nodes, 3x-ui/Xray, local proxy or DNS topology as part of monitor upgrades.
