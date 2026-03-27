---
name: pm2-management
description: Manage local development processes via PM2. Use when the user asks to start, stop, restart, check status of, or manage local dev services, or mentions PM2.
---

# PM2 Process Management

You have PM2 installed globally for managing local development processes. Use PM2 commands directly via the Bash tool.

## Quick Reference

| Action | Command |
|--------|---------|
| List all processes | `pm2 list` |
| Start a process | `pm2 start <script> --name <name>` |
| Stop a process | `pm2 stop <name>` |
| Restart a process | `pm2 restart <name>` |
| Delete a process | `pm2 delete <name>` |
| View logs | `pm2 logs <name>` |
| View logs (last N lines) | `pm2 logs <name> --lines 50` |
| Flush all logs | `pm2 flush` |
| Monitor (JSON) | `pm2 jlist` |
| Kill PM2 daemon | `pm2 kill` |

## DevDash Integration

[DevDash](https://github.com/dannypenrose/devdash) is a web dashboard running on port 3099 that provides a UI for PM2 management. Its API can also be used:

| Action | curl Command |
|--------|-------------|
| List processes | `curl http://localhost:3099/api/processes` |
| Start process | `curl -X POST http://localhost:3099/api/processes/<name>/start` |
| Stop process | `curl -X POST http://localhost:3099/api/processes/<name>/stop` |
| Restart process | `curl -X POST http://localhost:3099/api/processes/<name>/restart` |
| View logs | `curl http://localhost:3099/api/processes/<name>/logs` |
| Clear logs | `curl -X DELETE http://localhost:3099/api/processes/<name>/logs` |
| Kill port | `curl -X POST http://localhost:3099/api/system -H 'Content-Type: application/json' -d '{"action":"kill-port","port":3000}'` |
| Flush all logs | `curl -X POST http://localhost:3099/api/system -H 'Content-Type: application/json' -d '{"action":"flush-logs"}'` |

## Process Configuration

- DevDash config: `~/.devdash/config.json`
- PM2 scripts: `~/.devdash/scripts/<name>.sh`
- PM2 logs: `~/.pm2/logs/<name>-out.log` and `<name>-error.log`

## Starting Dev Processes

When asked to start a project's dev server, prefer using PM2 directly:

```bash
# For projects using pnpm
pm2 start "bash -c 'cd /path/to/project && exec pnpm run dev'" --name project-dev --no-autorestart

# Or use an existing DevDash shell script
pm2 start ~/.devdash/scripts/<name>.sh --name <name> --interpreter bash --no-autorestart
```

## Important Notes

- Always use `--no-autorestart` for dev servers to prevent restart loops on port conflicts
- Use `pm2 delete` (not `pm2 stop`) before restarting to avoid stale process configs
- Check `pm2 list` before starting to avoid duplicate processes
- If a port is in use: `lsof -ti :<port> | xargs kill -9`
- DevDash itself runs on port 3099 — do not start other services on this port
