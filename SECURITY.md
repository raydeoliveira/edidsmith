# Security Policy — EdidSmith

## Reporting a Vulnerability
Report security vulnerabilities privately via GitHub Security Advisories on `https://github.com/raydeoliveira/edidsmith`.

## Security & Hardware Safety Model
- **Zero external dependencies**: Built strictly against system frameworks (`Foundation`, `CoreGraphics`, `IOKit` on macOS; DRM sysfs/debugfs on Linux).
- **Zero telemetry**: Makes zero background network calls. Outbound HTTPS occurs only during explicit `license activate` requests when built in commercial mode.
- **Symlink & path validation**: Display override paths validate hex vendor/product IDs and check for symbolic links via `lstat` before disk writes, with atomic snapshots and timed `SIGINT`/`SIGTERM` rollback.
