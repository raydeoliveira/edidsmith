# Polar.sh Product Listing — EdidSmith Pro (macOS & Linux DRM)

> Use this copy when creating the EdidSmith Pro product in Polar.sh (`polar.sh`).
> Enable Polar's **License Keys** benefit (prefix: `EDID`, activation limit: `3` machines per buyer, expiration: perpetual).

---

## Product Title
**EdidSmith Pro — EDID 1.4 / DisplayID 2.1 Parser, Link-Budget Solver & Rollback Override CLI**

## Price & Billing Model
- **$29 USD** — One-time purchase (Perpetual License, v0.x updates included, up to 3 personal machines).
- **Free Core Tier** included in the same binary: `probe`, `edid inspect`, `edid budget`, `edid patch --dry-run`, `edid restore`, `quirks`, and read-only MCP tools (`probe_displays`, `inspect_edid`, `solve_link_budget`, `lookup_display_quirk`, `patch_edid` with `dry_run: true`).

## Tagline (1-sentence summary)
Decode EDID 1.4, CTA-861, and DisplayID 2.0/2.1 blocks, solve HDMI/DP/USB-C link budgets with VESA CVT-RB2, and apply display overrides with atomic snapshots and 15-second signal-safe rollback.

## Storefront Description

External 3D monitors, AR glasses, and 4K OLED TVs routinely ship with missing `3840x1080` Full-SBS DTDs, forced YCbCr 4:2:0 flags that blur text on macOS, missing HDMI 1.4 3D Frame Packing flags, or DisplayID 2.0/2.1 blocks that naive EDID editors overwrite.

**EdidSmith** is a zero-dependency native CLI and stdio MCP server built for humans and AI agents.

### What Is Included in Pro ($29 Perpetual)
- **Checksum-Valid EDID & DisplayID Patcher (`edid patch`)**:
  - `full-sbs-1080p` / `full-sbs-1080p-120` (`3840x1080` CVT-RB2 DTD for AR glasses and 3D displays)
  - `force-rgb444` (clears YCbCr feature bits and strips `0x0E`/`0x0F` YCbCr 4:2:0 blocks while preserving 18B DTD alignment)
  - `hdmi-3d-framepack` (injects HDMI 1.4 VSDB 3D Frame Packing, TAB, and SBS-Half)
  - `inject-vrr` (injects/updates DisplayID `0x25` Dynamic Video Timing Range Limits)
  - `inject-displayid-t7` (injects DisplayID `0x22` Type VII 20-byte timings for >655.35 MHz / >4095 px modes)
  - Always preserves Block 1 DisplayID (`0x70`) and base `0xFC`/`0xFD`/`0xFE`/`0xFF` descriptors.
- **Snapshot + 15s Rollback Installer (`edid apply`)**: Validates EDID and DisplayID inner+outer checksums before writing, checks paths with `lstat` against symlink traversal, snapshots prior overrides atomically, verifies byte-for-byte readback, and auto-reverts on timeout or `SIGINT`/`SIGTERM`.
- **Built-in AI Agent MCP Server (`edidsmith mcp`)**: Stdio JSON-RPC 2.0 (`2024-11-05`) + `--from-json -` UNIX pipeline composability.

### Free Core vs. Pro Comparison

| Feature | Free Core | Pro ($29 one-time) |
|---|---|---|
| `probe` (CoreGraphics/IOKit/SPI or `--sim` / `--fixture`) | Included | Included |
| `edid inspect` (EDID 1.4 / CTA-861 / DisplayID 2.0/2.1) | Included | Included |
| `edid budget` (CVT-RB2 & 15 HDMI/DP/USB-C standards + DSC 1.2) | Included | Included |
| `edid patch --dry-run` (checksum & slot verification) | Included | Included |
| `edid restore` (restore snapshot / reset override) | Included | Included |
| `quirks` & MCP `lookup_display_quirk` | Included | Included |
| `edid patch` binary & macOS override plist write | — | Included |
| `edid apply` safe installer with timed rollback | — | Included |
| `mcp` `patch_edid` with `dry_run: false` | — | Included |

### Requirements & Honest Limits
- **OS & Hardware**: macOS 14.0+ on Apple Silicon (`arm64`); Linux DRM sysfs/debugfs source support guarded under `#if os(Linux)`.
- **Permissions**: Writing macOS overrides under `/Library/Displays/Contents/Resources/Overrides` requires `sudo` and reconnecting the display or rebooting for WindowServer to reload. Linux live `edid_override` requires debugfs permissions (`root` / `CAP_SYS_ADMIN`) or staging `/lib/firmware/edid/<slug>.bin` with `drm.edid_firmware=...`.
- **Activation & Privacy**: Activates once via Polar.sh (`edidsmith license activate --key <KEY>`), caches locally with `0600` permissions and 14-day offline grace, and makes zero background network calls.

---

## Seller Launch Checklist
1. Create organization `raydeoliveira` on `polar.sh` (or set `EDIDSMITH_POLAR_ORG_ID`).
2. Create product **EdidSmith Pro** ($29 one-time).
3. Upload `dist/edidsmith-0.1.0-macos-arm64.tar.gz` and `dist/SHA256SUMS.txt`.
4. Enable **License Keys** benefit: prefix `EDID`, max `3` activations, no expiration.
