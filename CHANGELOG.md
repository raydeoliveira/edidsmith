# Changelog

All notable changes to EdidSmith are documented in this file.

## [0.1.0] - 2026-11-21

### Added
- Clean-room Swift 6 EDID 1.4, multi-block CTA-861, and DisplayID 2.0/2.1 (`0x70`) parser and dual inner+outer checksum validator (`F02`, `F03`).
- VESA CVT-RB2 timing synthesizer (`1 kHz` / `10 kHz` / `1000/1001` NTSC) and 15-standard HDMI/DP/USB-C link-budget solver with DSC 1.2 8 bpp floor (`F04`).
- Safe EDID/DisplayID patcher (`full-sbs-1080p`, `full-sbs-1080p-120`, `force-rgb444`, `hdmi-3d-framepack`, `inject-vrr`, `inject-displayid-t7`), 6-entry `QuirkCatalog`, and 6 golden + 3 negative binary fixtures (`F05`).
- Isolated `SPIAdapter` (`dlopen`/`dlsym` `IOAVService*`), `MacDisplayAdapter`, `LinuxDRMDisplayAdapter`, `SimDisplayAdapter`, `FileOverrideStorageAdapter` (`lstat` symlink guard), and `TerminalConfirmationAdapter` 15s `sigaction` watchdog (`F06`).
- `--from-json -` composability, stdio JSON-RPC 2.0 `mcp` server (`2024-11-05`, `lookup_display_quirk`), `PolarLicenseAdapter` (`-DOSS_EDITION` seam), and Trinity packaging (`SKILL.md`, `server.json`, Homebrew, Polar, `package_release.sh`) (`F07`).
