DOLI Auto Bond

Automatic bond compounding script for a DOLI producer node.

This script periodically checks the wallet balance and automatically adds new bonds when enough spendable DOLI is available.

The script runs using a systemd timer, includes cooldown protection, and records only successful bond increases.

Features

Automatic compounding of rewards

Multi-bond support per run

Configurable bonding thresholds

Cooldown protection to prevent repeated bonding

systemd timer integration

Success-only logging

Lock protection to prevent concurrent runs

Repository Contents

doli-auto-bond
Main automation script.

doli-auto-bond.service
systemd service used to execute the script.

doli-auto-bond.timer
systemd timer that runs the service periodically.

auto-bond.conf.example
Example configuration file.

README
Documentation file.

.gitignore
Prevents sensitive files from being committed.

Requirements

Linux server
systemd
DOLI node installed
Wallet already configured
Producer node running

Installation

Copy the script

sudo cp doli-auto-bond /usr/local/bin/doli-auto-bond
sudo chmod +x /usr/local/bin/doli-auto-bond

Create configuration directory

sudo mkdir -p /etc/doli

Copy the example configuration

sudo cp auto-bond.conf.example /etc/doli/auto-bond.conf

Edit the configuration

sudo nano /etc/doli/auto-bond.conf

Configuration Example

DOLI_BIN=/usr/bin/doli

MIN_SPENDABLE=11
BOND_AMOUNT_PER_BOND=10
MAX_BONDS_PER_RUN=5

MAX_BOND_COUNT=3000
MAX_TOTAL_BONDED=30000

MIN_SECONDS_BETWEEN_BONDS=60

Configuration Options

MIN_SPENDABLE
Minimum spendable balance required before bonding.

BOND_AMOUNT_PER_BOND
Amount of DOLI required per bond.

MAX_BONDS_PER_RUN
Maximum bonds the script can add in a single execution.

MAX_BOND_COUNT
Maximum bond count allowed by the network.

MAX_TOTAL_BONDED
Maximum total bonded DOLI allowed.

MIN_SECONDS_BETWEEN_BONDS
Cooldown period between bonding attempts.

Create Required Directories

The script requires directories for logs and cooldown tracking.

Create log directory

sudo mkdir -p /var/log/doli
sudo touch /var/log/doli/auto-bond-success.log
sudo chown atinoco:atinoco /var/log/doli/auto-bond-success.log

Create cooldown state directory

sudo mkdir -p /var/lib/doli
sudo chown atinoco:atinoco /var/lib/doli

Install systemd Service

sudo cp doli-auto-bond.service /etc/systemd/system/

Install systemd Timer

sudo cp doli-auto-bond.timer /etc/systemd/system/

Reload systemd

sudo systemctl daemon-reload

Enable the timer

sudo systemctl enable --now doli-auto-bond.timer

Verify the Timer

systemctl list-timers | grep doli

Example output

NEXT LEFT LAST UNIT
08:10:00 UTC 45s 08:09:00 UTC doli-auto-bond.timer

Run Manually

sudo systemctl start doli-auto-bond.service

Logs

Success Log

cat /var/log/doli/auto-bond-success.log

Example

2026-03-06 07:42:11 add-bond successful, added=1, bond_count=46

Watch log live

tail -f /var/log/doli/auto-bond-success.log

Service Logs

journalctl -u doli-auto-bond.service

Recent logs

journalctl -u doli-auto-bond.service -n 50

Watch logs live

journalctl -u doli-auto-bond.service -f

Check Timer Status

systemctl status doli-auto-bond.timer

Stop Automation

sudo systemctl disable --now doli-auto-bond.timer

Troubleshooting

Script does not run

chmod +x /usr/local/bin/doli-auto-bond

Cooldown active message

Cooldown active. Waiting X seconds before next bond attempt.

This prevents repeated bonding attempts.

Verify producer status

doli producer status

Security Notes

Do NOT commit the following files to the repository:

wallet files
private keys
/etc/doli/auto-bond.conf
log files

Only commit the example configuration file.

License

Private repository intended for personal validator operations.
