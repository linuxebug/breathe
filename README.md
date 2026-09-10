# Braethe Cloud VPS Deploy Bot

Braethe Cloud is a Discord VPS management bot by INFINITE LABS. It deploys and manages Docker-based VPS containers and provides SSHX web terminal access.

## Features

- Docker-based VPS deployment
- Support for 2GB, 4GB, 8GB, and 16GB RAM plans
- Ubuntu 22.04 support
- Debian 11 support
- SSHX web terminal access
- VPS start, stop, restart and reinstall
- User and admin VPS management
- Custom RAM tiers for admin-created VPS
- Expiration system with DM notifications (3d, 2d, 1d warnings)
- Automatic VPS shutdown on expiration
- SQLite database persistence
- Automatic Docker container management
- Systemd service support
- 24/7 operation with auto-restart

## VPS Plans

| RAM | CPU | Disk |
|---|---|---|
| 2GB | 1 Core | 10GB |
| 4GB | 2 Cores | 25GB |
| 8GB | 4 Cores | 50GB |
| 16GB | 8 Cores | 100GB |

## Requirements

- Ubuntu/Debian Linux VPS or server
- Python 3
- Docker
- Root access
- Discord Bot Token
- Discord User ID for admin access

## Installation

### 1. Install Docker

```bash
apt update -y
apt install -y docker.io
systemctl enable docker
systemctl start docker
docker --version
```

### 2. Clone the repository

```bash
git clone https://github.com/nxtinfinite481-png/vps-deploy-bot.git
cd vps-deploy-bot
```

### 3. Configure environment

```bash
cp braethe.env .env
nano .env
```

Example:

```env
TOKEN=YOUR_DISCORD_BOT_TOKEN
ADMIN_ID=paste your discord user id
BOT_STATUS_NAME=Braethe Cloud
WATERMARK=Braethe Cloud Team
DISCORD_SUPPORT_LINK=https://discord.gg/zC3xHcnj8r
DEFAULT_RAM=2g
DEFAULT_CPU=1
DEFAULT_DISK=10G
VPS_HOSTNAME=braethe-vps
```

### 4. Create the Python Virtual Environment

A project-local virtual environment is used so the bot does not depend on the system Python path of the VPS.

```bash
apt install -y python3-venv
python3 -m venv venv
```

### 5. Install Dependencies

Install all bot dependencies inside the virtual environment:

```bash
./venv/bin/pip install --upgrade pip
./venv/bin/pip install -r requirements.txt
```

### 6. Test the Bot

Before creating the systemd service, test the bot manually:

```bash
./venv/bin/python bot.py
```

If the bot starts successfully, press:

```text
Ctrl+C
```

to stop the test.

## Systemd Service

Create the systemd service:

```bash
nano /etc/systemd/system/braethe.service
```

Paste:

```ini
[Unit]
Description=Braethe Cloud VPS Discord Bot
After=network.target docker.service

[Service]
User=root
WorkingDirectory=/root/vps-deploy-bot
ExecStart=/root/vps-deploy-bot/venv/bin/python /root/vps-deploy-bot/bot.py
Restart=always
RestartSec=5
Environment=PYTHONUNBUFFERED=1

[Install]
WantedBy=multi-user.target
```

Save the file and run:

```bash
systemctl daemon-reload
systemctl enable braethe
systemctl restart braethe
```

Check the bot status:

```bash
systemctl status braethe --no-pager
```

View live logs:

```bash
journalctl -u braethe -f
```

If everything is working correctly, the bot should appear online in Discord.

## Default VPS Configuration

| Resource | Default |
|---|---|
| RAM | 2GB |
| CPU | 1 Core |
| Disk | 10GB |
| VPS Limit / User | 1 |
| Total VPS Limit | 50 |
| Hostname | braethe-vps |

## Supported Operating Systems

Braethe Cloud currently supports:

- Ubuntu 22.04
- Debian 11

## SSHX Access

Braethe Cloud uses SSHX for web-based VPS terminal access.

When a VPS is created, Braethe Cloud automatically:

1. Creates the Docker VPS container
2. Installs the required packages
3. Installs SSHX
4. Starts an SSHX session
5. Generates an SSHX web access link
6. Sends the access link to the user

The `/ssh` command can also be used to generate a new SSHX access link for an existing VPS.

## VPS Resources

Default VPS resources:

```text
RAM: 2GB
CPU: 1 Core
Disk: 10GB
```

Docker memory and CPU limits are applied when the VPS container is created.

## Expiration System

Admin-created VPS can have an expiration time set using the `/admin-create` command.

Expiration format:
- `4d` = 4 days
- `1w` = 1 week (7 days)
- `1m` = 1 month (30 days)

When a VPS is set to expire:
1. **3 days before expiry**: User receives a DM warning
2. **2 days before expiry**: User receives a DM warning
3. **1 day before expiry**: User receives a DM warning
4. **On expiry**: VPS is automatically stopped, user receives a DM with a Discord support link

Users can still view expired VPS information but cannot start them. Contact Discord support for renewal.

## Commands

### User Commands

```text
/create <os>
/list
/vps-info [vps_id]
/ssh [vps_id]
/start <vps_id>
/stop <vps_id>
/restart <vps_id>
/reinstall <vps_id> [os]
/delete <vps_id>
/about
/help
/logs <vps_id> [lines]
```

### Admin Commands

```text
/admin-create <user> <os> <ram> [expire]
/admin-manage <user> <vps> <action>
/admin-users
/admin-list
/admin-stats
/admin-vps-info <user> <vps>
/admin-logs <user> <vps> [lines]
/admin-del-user <user>
/admin-ban <user>
/admin-unban <user>
/admin-stop-all
```

### Additional Commands

```text
/about
/logs <vps_id> [lines]
/ping
/help
```

## VPS Management

Users can manage their VPS using:

```text
/start
/stop
/restart
/reinstall
/delete
```

SSHX access can be generated using:

```text
/ssh
```

VPS information can be viewed using:

```text
/vps-info
```

VPS list can be viewed using:

```text
/list
```

## Admin VPS Management

Administrators can create VPS instances with custom RAM tiers and expiration using:

```text
/admin-create <target_user> <os_type> <ram> [expire]
```

RAM choices: `2GB`, `4GB`, `8GB`, `16GB`

Disk is auto-assigned based on RAM selection.

Administrators can manage VPS instances using:

```text
/admin-list
/admin-vps-info
/admin-stop-all
/admin-del-user
```

When an admin deletes a user's VPS using `/admin-del-user`, the affected user receives a DM notification.

## Database

Braethe Cloud uses SQLite for persistent VPS and user data.

Database file:

```text
bot.db
```

The database is automatically created and maintained by the bot.

## Logs

Bot logs are stored in:

```text
bot.log
```

Systemd logs can be viewed with:

```bash
journalctl -u braethe -f
```

## Support

Join the Discord support server: https://discord.gg/zC3xHcnj8r

## Project Files

```text
vps-deploy-bot/
├── bot.py
├── requirements.txt
├── braethe.env
├── .env
├── bot.db
├── bot.log
└── venv/
```

## Developer

**Braethe Cloud**

**Version:** v2.0

**Developer:** INFINITE

**YouTube:** https://www.youtube.com/@infinite8labs

**GitHub:** https://github.com/nxtinfinite481-png

**Discord Support:** https://discord.gg/zC3xHcnj8r

Made by Braethe Cloud Team
