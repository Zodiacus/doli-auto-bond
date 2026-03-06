# DOLI Auto Bond

Automatic bond compounding script for a **DOLI producer node**.

This script periodically checks the wallet balance and automatically adds new bonds when enough spendable DOLI is available.

It is designed to run safely with **systemd timers**, includes **cooldown protection**, and only records **successful bond increases**.

---

# Features

- Automatic compounding of rewards
- Multi-bond support per run
- Configurable bonding thresholds
- Cooldown protection to prevent repeated bonding
- Systemd timer integration
- Success-only logging
- Lock protection to prevent concurrent runs

---

# Repository Contents


doli-auto-bond
doli-auto-bond.service
doli-auto-bond.timer
auto-bond.conf.example
README.md
.gitignore


File descriptions:

doli-auto-bond  
Main automation script.

doli-auto-bond.service  
Systemd service used to execute the script.

doli-auto-bond.timer  
Systemd timer that runs the service periodically.

auto-bond.conf.example  
Example configuration file.

---

# Requirements

- Linux server
- systemd
- DOLI node installed
- Wallet already configured
- Producer node running

---

# Installation

## 1. Copy the script

```bash
sudo cp doli-auto-bond /usr/local/bin/doli-auto-bond
sudo chmod +x /usr/local/bin/doli-auto-bond
2. Create configuration directory
sudo mkdir -p /etc/doli

Copy the example configuration:

sudo cp auto-bond.conf.example /etc/doli/auto-bond.conf

Edit the configuration:

sudo nano /etc/doli/auto-bond.conf
Configuration

Example config:

DOLI_BIN=/usr/bin/doli

MIN_SPENDABLE=11
BOND_AMOUNT_PER_BOND=10
MAX_BONDS_PER_RUN=5

MAX_BOND_COUNT=3000
MAX_TOTAL_BONDED=30000

MIN_SECONDS_BETWEEN_BONDS=60

Explanation:

MIN_SPENDABLE
Minimum spendable balance required before bonding.

BOND_AMOUNT_PER_BOND
DOLI required per bond.

MAX_BONDS_PER_RUN
Maximum bonds the script can add in a single run.

MAX_BOND_COUNT
Network limit for bonds.

MAX_TOTAL_BONDED
Network bonded amount limit.

MIN_SECONDS_BETWEEN_BONDS
Cooldown time between bonding attempts.

Create required directories

The script needs directories for logs and cooldown tracking.

sudo mkdir -p /var/log/doli
sudo touch /var/log/doli/auto-bond-success.log
sudo chown atinoco:atinoco /var/log/doli/auto-bond-success.log

Cooldown state directory:

sudo mkdir -p /var/lib/doli
sudo chown atinoco:atinoco /var/lib/doli
Install systemd service

Copy the service file:

sudo cp doli-auto-bond.service /etc/systemd/system/
Install timer

Copy the timer file:

sudo cp doli-auto-bond.timer /etc/systemd/system/

Reload systemd:

sudo systemctl daemon-reload

Enable the timer:

sudo systemctl enable --now doli-auto-bond.timer
Verify the timer
systemctl list-timers | grep doli

Example output:

NEXT                        LEFT       LAST                        UNIT
Fri 2026-03-06 08:10:00     45s        Fri 2026-03-06 08:09:00     doli-auto-bond.timer
Run manually

You can trigger a run manually with:

sudo systemctl start doli-auto-bond.service
Logs
Success log

Shows successful bond increases.

cat /var/log/doli/auto-bond-success.log

Example:

2026-03-06 07:42:11 add-bond successful, added=1, bond_count=46

Watch live:

tail -f /var/log/doli/auto-bond-success.log
Service logs

Systemd captures script output.

View logs:

journalctl -u doli-auto-bond.service

Recent logs:

journalctl -u doli-auto-bond.service -n 50

Watch live:

journalctl -u doli-auto-bond.service -f
Check timer status
systemctl status doli-auto-bond.timer
Stop automation

Disable the timer:

sudo systemctl disable --now doli-auto-bond.timer
Troubleshooting
Script will not run

Check permissions:

chmod +x /usr/local/bin/doli-auto-bond
Cooldown active

If cooldown is active you may see:

Cooldown active. Waiting X seconds before next bond attempt.

This prevents duplicate bond attempts.

Verify producer status
doli producer status
Security Notes

Do NOT commit:

wallet files

private keys

/etc/doli/auto-bond.conf

logs

Only commit the example config.
