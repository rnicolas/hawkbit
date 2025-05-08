# Hawkbit Update Server with Podman

This repository contains a `compose.yaml` file to set up the Hawkbit Update Server along with MariaDB and RabbitMQ using Podman.

---

## Prerequisites

### Podman or Docker

- If you are using Podman, ensure [compose is set up](https://podman-desktop.io/docs/compose/setting-up-compose).
- For additional help, refer to the [Podman documentation](https://podman.io/docs/).

---

## Steps to Run and Stop

### Step 1: Start the Services

Run the following command to start the services defined in `compose.yaml`:

```bash
podman compose --file compose.yaml up --detach
```

This will start the following containers:
- **MariaDB**: A database for Hawkbit.
- **RabbitMQ**: A message broker for Hawkbit.
- **Hawkbit Update Server**: The main update server application.

### Step 2: Verify the Services

Check the status of the running containers:

```bash
podman ps
```

You should see the `mariadb`, `rabbitmq`, and `hawkbit` containers running.

### Step 3: Access the Hawkbit Server

The Hawkbit Update Server will be accessible at [http://localhost:8080](http://localhost:8080). For LAN access, use the [machine's LAN IP](https://www.avg.com/en/signal/find-ip-address), not localhost.

The default user/password combination is `admin/admin`. These credentials should not be used in production.

### Step 4: Stop the Services

To stop the running containers, use:

```bash
podman compose --file compose.yaml down
```

---

## Configuration

### Environment Variables

The `compose.yaml` file includes environment variables for MariaDB and RabbitMQ. You can modify these variables as needed:

- **MariaDB**:
  - `MYSQL_ROOT_PASSWORD`
  - `MYSQL_DATABASE`
  - `MYSQL_USER`
  - `MYSQL_PASSWORD`
- **RabbitMQ**:
  - `RABBITMQ_DEFAULT_USER`
  - `RABBITMQ_DEFAULT_PASS`

### Volumes

The following volumes are used to persist data:
- `mariadb_data`: Stores MariaDB data.
- `hawkbit_data`: Stores Hawkbit data.

---

## WSL2 Mirrored Networking & Hyper-V Firewall Configuration for LAN Access

This guide explains how to configure WSL2 with mirrored networking and properly open firewall rules so services running inside Podman can be accessed from other devices on your LAN.

### Step 1: Configure WSL2 for Mirrored Networking

Create or edit the `.wslconfig` file (for e.g. `code $env:USERPROFILE/.wslconfig`):

```ini
[wsl2]
networkingMode=mirrored
ipv6=true
```

Then, restart WSL:

```powershell
wsl --shutdown
```

And restart the Podman machine:

```powershell
podman machine start
```

Note that mirrored networking and Hyper-V may behave differently on Wi-Fi vs Ethernet.

### Step 2: Configure Hyper-V Firewall Settings

Run the following command in a PowerShell window with admin privileges to allow inbound connections:

```powershell
Set-NetFirewallHyperVVMSetting -Name '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' -DefaultInboundAction Allow
```

### Step 3: Add a Firewall Rule

In the same Powershell mentioned above, create a new firewall rule to allow traffic on port 8080:

```powershell
New-NetFirewallRule -DisplayName "WSL 8080" -Direction Inbound -Protocol TCP -LocalPort 8080 -Action Allow -Profile Private,Domain
```
