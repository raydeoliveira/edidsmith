---
name: edidsmith
description: >-
  Clean-room EDID 1.4, CTA-861, and DisplayID 2.0/2.1 parser, VESA CVT-RB2 link-budget
  solver, and safe display override CLI and MCP server. Use when
  inspecting display EDID/DisplayID capabilities, solving HDMI/DP/USB-C link bandwidths,
  patching Full-SBS 3840x1080 / RGB 4:4:4 / HDMI 3D / VRR / DisplayID Type VII overrides,
  or looking up display quirks.
---

# Skill: EdidSmith (`edidsmith`)

## Install & Discover

The v0.1.0 CLI and MCP bundle run on Apple Silicon with macOS 14 or later. Linux and SteamOS backends are documented in the source project.

```bash
brew install raydeoliveira/tap/edidsmith
edidsmith --describe
```

## Safe Display & EDID Workflow
1. Probe attached external displays (or pass `--sim` / `--fixture <name>` without hardware):
   ```bash
   edidsmith probe --json
   edidsmith probe --fixture xreal-ar-glasses --json
   ```
2. Inspect EDID 1.4, CTA-861, and DisplayID 2.0/2.1 blocks:
   ```bash
   edidsmith edid inspect --fixture displayid21-5k-tiled --json
   ```
3. Check VESA CVT-RB2 timing and link bandwidth fit before patching:
   ```bash
   edidsmith edid budget --width 3840 --height 1080 --hz 60 --bpc 8 --chroma 444 --link usbc-2lane --json
   ```
4. Look up known display quirks offline:
   ```bash
   edidsmith quirks --vendor 3a4c --product 0574 --json
   ```
5. Dry-run EDID patch before writing files:
   ```bash
   edidsmith edid patch --fixture xreal-ar-glasses --preset full-sbs-1080p --dry-run --json
   ```
6. Apply with snapshot + readback verification + 15s auto-revert watchdog:
   ```bash
   edidsmith edid apply override.plist --display-vendor 3a4c --display-product 0574 --confirm-timeout 15 --json
   ```
7. Restore prior state at any time (free, zero license required):
   ```bash
   edidsmith edid restore --display-vendor 3a4c --display-product 0574 --json
   ```

## UNIX Composability (`--from-json -`)
Chain canonical sub-objects (`display_identity`, `solved_mode`, `target_surface`, `optical_profile`) across Spatial Wedge tools. Explicit CLI flags override piped JSON fields, and upstream `ok: false` envelopes fail closed (`exit 2`, `UPSTREAM_ENVELOPE_FAILED`):
```bash
edidsmith edid patch --fixture xreal-ar-glasses --preset full-sbs-1080p --dry-run --json \
  | edidsmith edid budget --from-json - --link hdmi21-frl3 --json
```

## Native MCP Mode
Run `edidsmith mcp` over stdio (JSON-RPC 2.0, `protocolVersion: "2024-11-05"`) to expose `probe_displays`, `inspect_edid`, `solve_link_budget`, `patch_edid` (`dry_run: true` default), and `lookup_display_quirk`.

## Exit Codes
- `0` ok | `1` runtime/rollback | `2` usage/validation/bandwidth/upstream-failed | `3` hardware unavailable | `4` permission/symlink/debugfs denied | `5` Pro license required (`edidsmith license activate --key <KEY>`) | `130` interrupted (`SIGINT`/`SIGTERM`) after automatic rollback
