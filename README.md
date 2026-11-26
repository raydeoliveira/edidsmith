# EdidSmith

> **Product Hub:** [raymonddeoliveira.com/software](https://www.raymonddeoliveira.com/software#edidsmith) · **Buy EdidSmith Pro ($29):** [Polar Checkout](https://buy.polar.sh/polar_cl_pIMhrQJ83UQ82DxtgU7wU4yRWDOm9KHFbH32k35gjKV) · **Complete 4-CLI Bundle ($49, save $57):** [Bundle Checkout](https://buy.polar.sh/polar_cl_KTtCY3iQC4bzjqp93aC6LCog8UZqzZzuDgkAs3BvF74) · **Verified MCP:** [Glama](https://glama.ai/mcp/servers/raydeoliveira/edidsmith) · **Free Core:** `brew tap raydeoliveira/tap && brew install edidsmith`

Clean-room EDID 1.4, CTA-861, and DisplayID 2.0/2.1 parser, VESA CVT-RB2 link-budget solver, display quirk catalog, and safe rollback override CLI and stdio MCP server for macOS Apple Silicon and Linux/SteamOS DRM.

Measured release binary (`macOS arm64`, stripped + ad-hoc codesigned): **907,312 bytes (~886 KB)**, **6.3 ms** median `--version` cold start across 20 runs, zero third-party dependencies.

## What It Does / What It Does Not Do

| Capability | Status | Notes |
|---|---|---|
| EDID 1.4 base block (`0x00..0x7F`) | Supported | Established/Standard timings, DTDs, `0xFC` name, `0xFD` Range Limits (+255 offsets & CVT), `0xFE`/`0xFF` |
| Multi-block CTA-861 (`0x02`) | Supported | Audio (`1`), Speaker (`4`), SVD (`2`, VIC 1..219 + native bit 7), HDMI 1.4 VSDB, HF-VSDB, `0x00/0x05/0x06/0x0E/0x0F/0x79` |
| DisplayID 2.0/2.1 (`0x70`) | Supported | Dual inner+outer checksums, `payloadLen <= 121`, `0x20, 0x21, 0x22, 0x23, 0x25, 0x27, 0x28, 0x2A` |
| VESA CVT-RB2 & Link-Budget Solver | Supported | `1 kHz` / `10 kHz`, `1000/1001` NTSC, 15 HDMI/DP/USB-C link standards, DSC 1.2 8 bpp floor |
| Safe EDID & DisplayID Patcher | Supported | `full-sbs-1080p`, `full-sbs-1080p-120`, `force-rgb444`, `hdmi-3d-framepack`, `inject-vrr`, `inject-displayid-t7` |
| Safe Display Override & 15s Rollback | Supported | `lstat` symlink guard, atomic snapshot, byte-for-byte readback check, `sigaction` (`sa_flags=0`) rollback |
| Built-in Quirk DB & Stdio MCP Server | Supported | 6-entry quirk DB, `--from-json -` composability, JSON-RPC 2.0 `mcp` (`2024-11-05`) |
| Kernel extensions / SIP disabling | Not used | Zero kexts or DriverKit extensions; relies on standard macOS override plists or Linux DRM overrides |
| Automatic WindowServer reload | Not supported | Writing macOS `/Library/Displays/Contents/Resources/Overrides` requires `sudo` and display reconnect/reboot |

## Install & Uninstall

The Homebrew package runs on Apple Silicon with macOS Sonoma (14) or later. Inspect a bundled DisplayID fixture without connecting a display.

```sh
brew install raydeoliveira/tap/edidsmith
edidsmith edid inspect --fixture displayid21-5k-tiled --json
```

The release page provides the macOS archive, MCP bundle and SHA-256 checksums. [Version 0.1.0 downloads](https://github.com/raydeoliveira/edidsmith/releases/tag/v0.1.0)

### Uninstall

If you applied a display override, restore it with `edidsmith edid restore` before uninstalling.

```sh
brew uninstall edidsmith
```

The optional license cache and override snapshots are stored in `~/.config/edidsmith/`.

## Quick Start

### Human CLI
```bash
# 1. Inspect an EDID / DisplayID 2.1 fixture (or live attached display with `probe`)
edidsmith --version
# edidsmith 0.1.0 (commercial, arm64)

edidsmith edid inspect --fixture displayid21-5k-tiled
edidsmith quirks --vendor 3a4c --product 0574
```

### AI-Agent `--json` & `--from-json -` Usage
```bash
edidsmith edid budget --width 3840 --height 1080 --hz 60 --link usbc-2lane --json
```
Measured output:
```json
{"data":{"bpc":8,"chroma":"444","dsc_enabled":false,"effective_required_gbps":6.271,"fits":true,"link":"usbc-2lane","link_capacity_gbps":12.96,"recommendation":"Fits usbc-2lane uncompressed (48.4% link utilization).","required_gbps":6.271,"solved_mode":{"bpc":8,"chroma":"444","fits":true,"height":1080,"pixel_clock_khz":261310,"refresh_hz":60,"stereo_layout":null,"width":3840},"timing":{"h_blank":80,"h_front_porch":8,"h_sync_positive":true,"h_sync_width":32,"height":1080,"interlaced":false,"pixel_clock_khz":261310,"preferred":false,"refresh_hz":60,"source":"cvt-rb2","stereo_mode":null,"v_blank":31,"v_front_porch":1,"v_sync_positive":false,"v_sync_width":8,"width":3840},"utilization_percent":48.4},"error":null,"ok":true,"schema_version":1}
```

```bash
edidsmith quirks --vendor 3a4c --product 0574 --json
```
Measured output:
```json
{"data":{"count":1,"matches":[{"category":"ar-glasses","known_issues":["macOS suppresses 3840x1080 Full-SBS DTD over USB-C DP Alt Mode without an explicit CVT-RB2 override.","Some USB-C 2-lane hubs restrict high-refresh 3840x1080@120Hz."],"manufacturer_code":"NRL","model_name":"XREAL Air / One AR Glasses","notes":"Verify 2-lane vs 4-lane USB-C DP Alt Mode budget before applying 120 Hz Full-SBS.","product_id_hex":"0574","quirk_id":"xreal-full-sbs","recommended_link":"usbc-4lane","recommended_presets":["full-sbs-1080p","full-sbs-1080p-120"],"vendor_id_hex":"3a4c"}]},"error":null,"ok":true,"schema_version":1}
```

Pipe outputs across tools via `--from-json -` (upstream `ok: false` fails closed with exit `2` `UPSTREAM_ENVELOPE_FAILED`; explicit CLI flags override piped fields):
```bash
edidsmith edid patch --fixture xreal-ar-glasses --preset full-sbs-1080p --dry-run --json \
  | edidsmith edid budget --from-json - --link hdmi21-frl3 --json
```

Start the stdio JSON-RPC 2.0 MCP server (`2024-11-05`):
```bash
edidsmith mcp
```

## Safety & Rollback

1. **Probe & Validate First**: `probe` probes external displays only by default (`exit 3` if absent; pass `--sim` or `--fixture <name>` for hardware-free runs). `EDIDParser` validates both outer 128B block checksums and DisplayID `0x70` inner checksums before patching or applying.
2. **STW-003 Patcher Guards**: Never overwrites Block 1 DisplayID (`0x70`), never clobbers `0xFC`/`0xFD`/`0xFE`/`0xFF` descriptors, rejects CTA DBC overflow (`> 123B`), and preserves `dtdOffset == 0 -> 4` alignment.
3. **Symlink Guard & Atomic Snapshots**: `FileOverrideStorageAdapter` checks paths with `lstat` (`exit 4` on symlink traversal), snapshots existing overrides before writing, and verifies byte-for-byte readback.
4. **Non-TTY & 15s Signal Watchdog**: `edid apply` fails closed (`exit 2`) if stdin is non-interactive without `--yes`. Interactive runs wait up to `--confirm-timeout <sec=15>` using non-restarting `sigaction` (`sa_flags = 0`) + `poll()`; timeout (`exit 1`) or `SIGINT`/`SIGTERM` (`exit 130`) restores the exact prior override.
5. **Hardware Test Scope**: All EDID/DisplayID codecs, link budgets, multi-display matching, Linux DRM sysfs/debugfs helpers, `lstat` symlink guards, `openpty` `SIGINT` rollback (`exit 130`), Polar loopback HTTP licensing, CLI composability, and MCP stdio behavior pass automated tests on Apple Silicon macOS. Physical external 3D monitors and Linux kernel `debugfs` nodes were not attached on the build host; test live overrides with `--dry-run` first.

## Exit Codes & CLI Reference

Run `edidsmith --help` for human usage or `edidsmith --describe` for the JSON capability manifest.

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | Runtime or rollback failure |
| `2` | Usage, invalid EDID, bandwidth exceeded, non-TTY without `--yes`, or `UPSTREAM_ENVELOPE_FAILED` |
| `3` | External hardware or SPI symbol unavailable |
| `4` | Permission, symlink (`lstat`), or Linux debugfs denied |
| `130` | Interrupted (`SIGINT`/`SIGTERM`) after automatic rollback |

## License & Security

- Security policy: [SECURITY.md](SECURITY.md)
