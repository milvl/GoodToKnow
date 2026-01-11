# Docker Files Reduction

In case the Docker files take up too much space on the disk due to repeated builds or similar it is advisable to clean up the growing Docker disk partition. 

>This problem usually occurs when the Docker data is managed with WSL2 by Windows itself.

The following Powershell script can help:

```powershell
# Show what WSL distros exist (so you can confirm Docker’s distros are present: docker-desktop, docker-desktop-data).
wsl -l -v

# Show Docker disk usage before cleanup (optional baseline).
docker system df

# Delete *everything* Docker can delete: stopped containers, unused networks, *all* unused images, *all* unused volumes, and build cache.
docker system prune --all --volumes --force

# Also remove all unused build cache explicitly (often redundant, but helps ensure builder cache is gone).
docker builder prune --all --force

# Show Docker disk usage after prune (optional verification).
docker system df

# TRIM free space inside Docker’s WSL filesystem (helps VHDX compaction be more effective).
wsl -d docker-desktop-data -u root -- fstrim -av

# Shut down WSL to ensure VHDX files are not mounted/locked (safe: stops instances, does not delete distros).
wsl --shutdown

# (Optional) Enable WSL sparse mode for Docker’s data distro so it can automatically reclaim space going forward.
# Note: sparse mode support was added to WSL and can be toggled per-distro. 
wsl --manage docker-desktop-data --set-sparse true

# Shut down WSL again after changing sparse mode (ensures state is clean before compaction).
wsl --shutdown

# Find Docker Desktop’s VHDX files under the default Docker Desktop WSL storage path.
$dockerVhdxFiles = Get-ChildItem -Path "$env:LOCALAPPDATA\Docker\wsl" -Recurse -Filter "*.vhdx" -ErrorAction SilentlyContinue

# Filter likely Docker data disks (covers docker_data.vhdx and/or ext4.vhdx depending on Docker Desktop/WSL versions).
$targets = $dockerVhdxFiles | Where-Object { $_.Name -match 'docker_data\.vhdx|ext4\.vhdx' }

# Display the candidate VHDX files (so you can see exactly what will be compacted).
$targets | Select-Object FullName, Length

# (Optional but recommended) Backup the VHDX files before compacting (can be large; adjust destination as needed).
$backupDir = Join-Path $env:USERPROFILE "Desktop\docker-vhdx-backup"
New-Item -ItemType Directory -Path $backupDir -Force | Out-Null
$targets | ForEach-Object { Copy-Item -Path $_.FullName -Destination $backupDir -Force }

# Try Hyper-V Optimize-VHD first (fastest/cleanest when available on your Windows edition).
Import-Module Hyper-V -ErrorAction SilentlyContinue

# Compact each target VHDX using Optimize-VHD (requires Hyper-V module; if missing, use diskpart fallback below).
$targets | ForEach-Object { Optimize-VHD -Path $_.FullName -Mode Full }

# --- Diskpart fallback (use if Optimize-VHD is not available or fails) ---
# Create a temporary diskpart script that compacts each target VHDX read-only.
$diskpartScript = Join-Path $env:TEMP "compact-docker-vhdx.txt"
@(
  $targets | ForEach-Object {
    "select vdisk file=""$($_.FullName)"""
    "attach vdisk readonly"
    "compact vdisk"
    "detach vdisk"
  }
  "exit"
) | Set-Content -Path $diskpartScript -Encoding ASCII

# Run diskpart with the generated script to compact the VHDX files.
diskpart /s $diskpartScript

# Remove the temporary diskpart script (cleanup).
Remove-Item -Path $diskpartScript -Force -ErrorAction SilentlyContinue

# Show the VHDX sizes after compaction (verify reduction on disk).
$targets = Get-ChildItem -Path "$env:LOCALAPPDATA\Docker\wsl" -Recurse -Filter "*.vhdx" -ErrorAction SilentlyContinue | Where-Object { $_.Name -match 'docker_data\.vhdx|ext4\.vhdx' }
$targets | Select-Object FullName, Length
```
