# Desktop Factory
A reproducible, disposable Linux desktop environment generator powered by cloud-init, KVM, and Flatpak.

This project creates ephemeral Rocky Linux desktops on demand. Each desktop is:

- Stateless — all changes are discarded on shutdown
- Reproducible — every run starts from a verified base image
- Isolated — GUI apps run inside a sandboxed VM
- Portable — profiles define the desktop’s purpose declaratively
- Minimal — no custom images, no drift, no snowflakes

---

## Quickstart Guide

See QUICKSTART.md

---

## Features

- Launch a fresh Rocky Linux desktop in seconds
- Cloud-init–driven provisioning
- Flatpak-based GUI apps (Brave, Slack, Telegram, VSCodium, etc.)
- Xfce desktop environment
- RDP access via FreeRDP
- Snapshot-mode VMs (no persistence)
- Clean, modular profile system
- Verified base images (checksum-checked)

---

## Repository Structure

```
desktop-factory/
├── bin/
│   ├── desktop          # Main entrypoint
│   ├── fetch-base       # Download + verify Rocky base image
│   ├── make-seed        # Render cloud-init templates
│   ├── launch           # Launch ephemeral VM
│   ├── rdp              # Connect to a running VM
│   ├── host-setup       # Prepare the VM host
│   └── user-setup       # Prepare the user’s machine (RDP client)
│
├── profiles/
│   ├── common.yaml
│   ├── browser.yaml
│   ├── messaging.yaml
│   └── dev.yaml
│
├── cloud-init/
│   ├── templates/
│   │   ├── user-data.j2
│   │   └── meta-data.j2
│   └── generated/
│
└── images/
    ├── base/
    └── tmp/
```

---

## Host Requirements

Run this on the machine that will launch the desktops.

- Rocky Linux 9 (or similar RHEL-compatible host)
- KVM virtualization support
- libvirt + qemu-kvm
- genisoimage
- python3 + jinja2 + jinja2-cli
- yq
- curl / wget
- FreeRDP (optional if connecting from another machine)

Install everything:

```
bin/host-setup
```

Log out and back in so libvirt permissions apply.

---

## User Requirements

If the user machine is separate from the host, install an RDP client:

```
bin/user-setup
```

Connect using:

```
bin/rdp <profile>
```

---

## Usage

### List profiles

```
bin/desktop list
```

### Spawn a disposable desktop

```
bin/desktop spawn browser
```

This will:

1. Verify the Rocky base image
2. Render cloud-init templates
3. Create a seed ISO
4. Launch a VM in snapshot mode
5. Print the RDP connection info

### Connect to the desktop

```
bin/rdp browser
```

### Shutdown

Just close the VM or shut it down from inside the desktop.  
All changes are discarded automatically.

---

## Profiles

Profiles define what each desktop contains.  
They inherit from `common.yaml`.

Examples:

### browser.yaml
- Brave browser (Flatpak)
- Xfce desktop
- xrdp enabled

### messaging.yaml
- Slack, Signal, Telegram (Flatpak)

### dev.yaml
- Git (RPM)
- VSCodium (Flatpak)

To create a new profile:

1. Add a YAML file under `profiles/`
2. Define `packages:` and/or `runcmd:`
3. Launch it:

```
bin/desktop spawn <name>
```

---

## How It Works

1. Base image downloaded + checksum verified
2. Profile merged with common.yaml
3. Jinja2 templates rendered
4. Seed ISO created
5. VM launched in snapshot mode
6. Cloud-init provisions the system
7. RDP becomes available

---

## Security Notes

- Base images are always checksum-verified
- VMs run in snapshot mode (no persistence)
- Flatpak apps run sandboxed
- No custom images or long-lived state

---

## Future Enhancements

- Per-profile CPU/RAM overrides
- Optional bridged networking
- Additional desktop environments
- macOS/Windows user-setup scripts
- Profile-specific post-launch hooks

---

## License

See LICENSE file
