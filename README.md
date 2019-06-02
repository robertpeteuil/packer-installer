# Installer for HashiCorp Packer - Automated Installation

## Automatically Download, Extract and Install Latest or Specific Version

[![release](https://img.shields.io/github/release/robertpeteuil/packer-installer.svg?colorB=2067b8)](https://github.com/robertpeteuil/packer-installer)
[![bash](https://img.shields.io/badge/language-bash-89e051.svg?style=flat-square)](https://github.com/robertpeteuil/packer-installer)
[![license](https://img.shields.io/github/license/robertpeteuil/packer-installer.svg?colorB=2067b8)](https://github.com/robertpeteuil/packer-installer)

---

The **packer-install** script automates the process of downloading and installing Packer.  It provides an ideal method for installing on new hosts, installing updates and downgrading if necessary.

This script detects the latest version, OS and CPU-Architecture and allows installation to local or system locations.  Optional parameters allow installing a specific version and installing to /usr/local/bin without prompting.

Options:

- `-i VERSION`:  Install specific version
- `-a`:          Automatically use `sudo` to install to /usr/local/bin
  - allows for unattended installation via scripts or CD tools
  - can be set as default behavior by uncommenting line 14 (`sudoInstall=true`)
  - sudo password may be required unless NOPASSWD is enabled
- `-c`:          leave binary in working directory (for CI/DevOps use)
- `-h`:          help
- `-v`:          display version

This installer is similar to my [Terraform Installer](https://github.com/robertpeteuil/terraform-installer) and [Vault Installer](https://github.com/robertpeteuil/vault-installer)

## Install

Download Installer

``` shell
curl -LO https://raw.github.com/robertpeteuil/packer-installer/master/packer-install.sh
chmod +x packer-install.sh
```

## Use

### Run local installer

``` shell
./packer-install.sh

# usage: packer-install.sh [-i VERSION] [-a] [-c] [-h] [-v]
#      -i VERSION	: specify version to install in format '' (OPTIONAL)
#      -a		: automatically use sudo to install to /usr/local/bin
#      -c		: leave binary in working directory (for CI/DevOps use)
#      -h		: help
#      -v		: display vault-install.sh version
```

### Express install via `iac.sh` or `https://iac.sh` (my bootstrap server)

``` shell
curl iac.sh/packer | sh   # run without '| sh' to view & verify script
```

## System Requirements

- System with Bash Shell (Linux, macOS, Windows Subsystem for Linux)
- `unzip` - packer downloads are in zip format
- `curl` or `wget` - script will use either one to retrieve metadata and download

Optional

- `jq` - if installed, latest version parsed from hashicorp downloads
  - Useful if latest github release differs from version on hashicorp downloads
  - Avoids github api limit of 60 requests per hour (unauthenticated)

## Script Process Details

- Determines Version to Download and Install
  - Uses Version specified by `-i VERSION` parameter (if specified)
  - Otherwise determines Latest Version
    - If `jq` installed parse version from hashicorp downloads
    - Otherwise use GitHub API to retrieve latest tag
- Calculates Download URL based on Version, OS and CPU-Architecture
- Verifies URL Validity before Downloading in Case:
  - VERSION incorrectly specified with `-i`
  - Download URL Format Changed on packer Website
- Determines Install Destination
  - Performed before Download/Install Process in case user selects `abort`
- Installation Process
  - Download, Extract, Install, Cleanup and Display Results

### CPU Architecture Detection

CPU architecture is detected for each OS accordingly:

- Linux / Windows (WSL since this is a Bash script)
  - detected with `lscpu` or by inspecting `/proc/cpuinfo`
- macOS - uses Default Arch `amd64` as it's the only version available on macOS
- Default Value - `amd64`

## Disclaimer

I am a HashiCorp employee, but this is a personal project and not officially endorsed or supported by HashiCorp.

## License

Apache 2.0 License - Copyright (c) 2019    Robert Peteuil
