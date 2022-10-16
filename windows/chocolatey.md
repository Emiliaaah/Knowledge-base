# Chocolatey

Chocolatey is a machine-level, command-line package manager and installer for windows software.

Project Homepage: [Chocolatey](https://chocolatey.org) 
Documentation: [Chocolatey Docs](https://docs.chocolatey.org/en-us/)
Packages: [Chocolatey Packages](https://community.chocolatey.org/packages)

---
## Installation

> [!WARNING] Make sure you're using powershell in administrator mode

1. Check the Execution Policy

Run `Get-ExecutionPolicy`. If it returns `Restricted`, then run `Set-ExecutionPolicy AllSigned` or `Set-ExecutionPolicy Bypass -Scope Process`.

2. Install Chocolatey

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

3. Check if you install chocolatey

```powershell
choco -?
```

---

## Command cheet sheat

#### Install a package
```powershell
choco install <package>
```

#### Upgrade a package
```powershell
choco upgrade <package>
```