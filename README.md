## WSL2 Mirrored Networking & Hyper-V Firewall Configuration for LAN Access

This guide explains how to configure WSL2 with mirrored networking and properly open firewall rules so services running inside Podman can be accessed from other devices on your LAN.

---

## Step 1: Configure WSL2 for Mirrored Networking

Create or edit this file `.wslconfig`:

```
[wsl2]
networkingMode=mirrored
ipv6=true
```

## Step 2: 

Run the following command in PowerShell window with admin privileges to Configure Hyper-V firewall settings to allow inbound connections:

`Set-NetFirewallHyperVVMSetting -Name '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' -DefaultInboundAction Allow`

## Step 3:

`New-NetFirewallRule -DisplayName "WSL 8080" -Direction Inbound -Protocol TCP -LocalPort 8080 -Action Allow -Profile Private,Domain`
