# Custom Debian Live ISO Builder

[![Build Custom Debian Live ISO](https://github.com/[username]/[repo-name]/actions/workflows/build-iso.yml/badge.svg)](https://github.com/[username]/[repo-name]/actions/workflows/build-iso.yml)

This repository contains configurations and workflows to automatically build a custom Debian-based live ISO using GitHub Actions and Debian's `live-build` system.

## Overview

The automated build system creates bootable ISO images based on Debian sid with:

- A configured desktop environment (XFCE by default)
- Custom package selection
- Live boot and installation capabilities
- Automated CI/CD process via GitHub Actions

## How It Works

The GitHub Actions workflow automates the entire build process:

1. Sets up a build environment on GitHub's Ubuntu runners
2. Installs necessary dependencies for ISO creation
3. Configures the live system based on repository settings
4. Builds a complete bootable ISO image
5. Uploads the resulting ISO as an artifact
6. Creates GitHub releases for tagged commits

## Repository Structure

```
repository/
├── .github/
│   └── workflows/
│       └── build-iso.yml      # The CI workflow file
├── auto/
│   ├── config                 # Main live-build configuration script
│   └── build                  # (Optional) Custom build script
├── config/
│   ├── package-lists/        
│   │   ├── desktop.list.chroot    # Desktop environment packages
│   │   └── kernel.list.chroot     # Kernel and firmware packages
│   ├── hooks/                 # Custom build hooks
│   ├── includes.chroot/       # Files to include in the live system
│   └── ... (other live-build configs)
└── README.md
```

## Customization Options

### Package Selection

Create or modify files in `config/package-lists/` ending with `.list.chroot` to customize installed packages:

```
# config/package-lists/desktop.list.chroot
task-xfce-desktop
firefox-esr
libreoffice
```

### System Configuration

Add custom configuration scripts in `config/hooks/`:

```bash
# config/hooks/live/01-custom-configuration.hook.chroot
#!/bin/sh
echo "Applying custom configurations..."
# Configuration commands here
```

### File Inclusion

Place files in `config/includes.chroot/` to have them copied to the live system:

```
config/includes.chroot/etc/skel/Desktop/welcome.desktop  # Adds desktop icon for all users
config/includes.chroot/usr/local/bin/custom-script       # Adds custom script to the system
```

## Workflow Triggers

The ISO build will be triggered by:

- **Push events** to `main` or `master` branches (excluding documentation changes)
- **Pull requests** to `main` or `master` branches
- **Manual triggers** through GitHub Actions interface
- **Tagged releases** (which also create GitHub releases with the ISO)

## Build Artifacts

After each successful build, the workflow produces:

- A bootable ISO file named `[repo-name]-live-[date].iso`
- SHA256 checksum file for verification
- Build logs for troubleshooting

These artifacts are available for download from the GitHub Actions run page for 30 days (logs for 7 days).

## Advanced Usage

### Manual Builds with Debugging

You can manually trigger builds from the Actions tab with an optional debugging session:

1. Go to the Actions tab in your repository
2. Select "Build Custom Debian Live ISO" workflow
3. Click "Run workflow"
4. Enable "Debug session" if needed
5. Click "Run workflow"

The debug option enables a tmate session that gives you SSH access to the build environment.

### Creating Releases

To create an official release with the ISO:

1. Create and push a git tag:
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```

2. The workflow will automatically build the ISO and create a GitHub release with the ISO file attached.

## Troubleshooting

If builds fail, check:

- The uploaded `build-logs` artifact for specific error messages
- Common issues include:
  - Insufficient disk space on GitHub runners
  - Network connectivity problems to Debian repositories
  - Invalid configuration in your custom settings
  - Missing dependencies in your package lists

## Configuration Parameters

The default `auto/config` script sets up a Debian sid-based system with:

- Architecture: amd64
- ISO format: hybrid (works with USB and optical media)
- Components: main, contrib, non-free, non-free-firmware
- Live installer included
- Security and update repositories enabled
- Default locale: en_US.UTF-8

## Resource Requirements

- The build process requires significant resources:
  - Build time: Up to 90 minutes (timeout set to prevent runaway builds)
  - Disk space: ~15-20GB during build process
  - Memory: ~2GB minimum

## License

This configuration is provided under [Your License Here].

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

---

**Note:** This project uses the official Debian live-build system. For more details on customizing the build process, see the [Debian Live Manual](https://live-team.pages.debian.net/live-manual/).# live-build
