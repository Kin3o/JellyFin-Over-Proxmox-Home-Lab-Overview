# Proxmox Jellyfin Home Lab Overview

This project documents a working Jellyfin media server built inside a dedicated Debian virtual machine on Proxmox VE. The goal was to create an approachable first media-server lab while keeping the Proxmox host clean and separating the application from the hypervisor.

The completed local deployment can serve media to devices on the GL.iNet LAN. A freely licensed copy of *Big Buck Bunny* was added as a test movie, identified by Jellyfin, and played successfully.

> **Project status:** The local Jellyfin deployment is verified. Permanent NAS storage, backups, remote access, and hardware-accelerated transcoding are planned but not yet implemented.

## Repository navigation

| Document | Purpose |
|---|---|
| [01-Jellyfin-Over-Proxmox-Home-Lab-Overview](https://github.com/Kin3o/JellyFin-Over-Proxmox-Home-Lab-Overview) | Project overview, architecture, verified results, and current status |
| [02-Jellyfin-Server-Build-Walkthrough](https://github.com/Kin3o/02-Jellyfin-Server-Build-Walkthrough) | Complete beginner-friendly installation and configuration procedure |
| [03-Jellyfin-Troubleshooting-Lab](https://github.com/Kin3o/03-Jellyfin-Troubleshooting-Lab) | Problems encountered, causes, solutions, and lessons learned |
| [04-Jellyfin-Future-Plans](https://github.com/Kin3o/04-Jellyfin-Future-Plans) | NAS storage, backups, remote access, and other planned improvements |

## Final architecture

The Proxmox host was originally assigned `192.168.3.34` behind an OPNsense lab interface. That routed arrangement was not proven reliable during this project. The host was moved directly onto the GL.iNet LAN, and the final working arrangement is shown below.

```text
GL.iNet router
192.168.8.1
    |
    +-- Proxmox VE host
        192.168.8.3/24
        https://192.168.8.3:8006
            |
            +-- VM 100: Debian 13.7 + Jellyfin 12.1
                192.168.8.4/24
                http://192.168.8.4:8096
```

These are private lab addresses. Anyone following this project must substitute addresses that match their own network.

## Hardware

| Component | Configuration | Status |
|---|---|---|
| Computer | Dell OptiPlex 5070 | Verified |
| Processor | Intel Core i5; exact model unknown | Not verified beyond the i5 family |
| System memory | 16 GB RAM | Verified |
| Proxmox storage | 128 GB SATA drive | Verified |

The exact processor model and integrated GPU have not been identified. Intel Quick Sync support must therefore be verified before hardware acceleration is configured.

## Software and VM configuration

| Component | Configuration | Status |
|---|---|---|
| Hypervisor | Proxmox VE 9.2.2 | Verified |
| VM ID and name | `100` / `jellyfin` | Verified |
| Guest operating system | Debian 13.7, no graphical desktop | Verified |
| CPU allocation | 4 virtual CPU cores | Verified |
| Memory | 8 GB final allocation | Verified |
| VM system disk | 32 GB on `local-lvm` | Verified |
| Virtual network | VirtIO adapter attached to `vmbr0` | Verified |
| Guest integration | QEMU Guest Agent enabled | Verified |
| Automatic startup | Enabled | Verified |
| Media server | Jellyfin 12.1 | Verified |
| Hardware acceleration | `None` | Verified |

## Network services

| Device or service | Address | Purpose |
|---|---|---|
| GL.iNet router | `192.168.8.1` | LAN gateway |
| Proxmox host | `192.168.8.3/24` | Hypervisor management |
| Proxmox web interface | `https://192.168.8.3:8006` | Proxmox administration |
| Jellyfin VM | `192.168.8.4/24` | Static Debian VM address |
| Jellyfin web interface | `http://192.168.8.4:8096` | Local media access |
| SSH | TCP `22` on the VM | Command-line administration |

Automatic router port mapping was disabled, and no public port forward was created for TCP `8096`.

## Jellyfin libraries

| Library | Current path | Status |
|---|---|---|
| Movies | `/srv/media/movies` | Created and tested |
| TV Shows | `/srv/media/tv` | Created |
| Music | `/srv/media/music` | Created |

These paths currently reside on the VM's 32 GB system disk. They are suitable for testing only and are not the planned permanent media-storage location.

## Verified results

- Debian 13.7 installed successfully without a desktop environment.
- SSH access worked with a normal Debian account followed by `su -` for root access.
- QEMU Guest Agent was installed and enabled.
- Jellyfin 12.1 installed from the verified official Debian installation script.
- Jellyfin started automatically and responded on TCP `8096`.
- Movies, TV Shows, and Music libraries were created.
- *Big Buck Bunny* was downloaded from Blender's official server, identified, given artwork and metadata, and played successfully.
- Public router port forwarding remained disabled.

## Skills demonstrated

- Proxmox virtual-machine provisioning
- Linux bridge and VirtIO networking
- Debian server installation and package management
- Linux service management with `systemctl`
- SSH administration from Windows PowerShell
- Linux directory ownership and permissions
- Checksum verification before running a privileged installer
- Jellyfin library and metadata configuration
- Structured troubleshooting and validation

## Current limitations

- The 32 GB VM disk is not appropriate for a permanent media collection.
- NAS storage has not been selected, mounted, or tested.
- Proxmox and Jellyfin backup restores have not been tested.
- The exact Intel i5 model is unknown.
- GPU access and Intel Quick Sync have not been configured.
- Hardware acceleration remains disabled.
- Tailscale remote access has not been deployed.
- The earlier OPNsense routed design was not proven fixed; the working build uses the direct GL.iNet connection.
- Media-directory ownership was configured during the build but not independently audited afterward.

## Future direction

The next major phase is moving the media library to a NAS while keeping Jellyfin's operating system, database, and configuration separate from the media files. Backup testing, non-administrator users, client testing, Tailscale, and possible Intel Quick Sync support will follow.

See [04-Jellyfin-Future-Plans](https://github.com/Kin3o/04-Jellyfin-Future-Plans) for the prioritized roadmap.

