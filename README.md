# ro-grf-repack

Repacks the kRO 2026 client `data.grf` (new `0x300` "Event Horizon" container) into the
classic `0x200` format that roBrowser / RemoteClient can read.

Why a Windows runner: `GrfCL.exe` is a 32-bit .NET assembly. On the VPS it runs under Wine
and cannot seek past 4 GiB (the 2026 file table starts at offset 4.58 GB), so every saveAs /
extract attempt dies with `System.IO.IOException: Invalid handle`. On a 64-bit Windows
runner the same AnyCPU assembly runs as a 64-bit process and handles the file fine.

## Usage

1. Actions -> **grf-repack** -> *Run workflow* (defaults point at the 2026 client archive).
2. The job downloads the archive, extracts `Data2026/data.grf`, installs GRFEditor silently,
   repacks to `0x200`, verifies the output starts with `Master of Magic`, then splits it into
   ~1.4 GB parts and publishes them as release assets.
3. Download the parts and join them:

```bash
gh release download <tag> --pattern "data_0200_parts.7z.*"
7z x data_0200_parts.7z.001     # joins the volumes and extracts data_0200.grf
```
