# WireGuard Server Setup

## Prerequisites

- SSH access to the server
- WireGuard installed on the server

Install WireGuard with:
```bash
sudo apt update && sudo apt install wireguard
```

## Instructions

### Step 1: Generate Keys

SSH into your server and generate WireGuard keys:

```bash
sudo mkdir -p /etc/wireguard
wg genkey | sudo tee /etc/wireguard/privatekey | wg pubkey | sudo tee /etc/wireguard/publickey
```

### Step 2: Configure VPN

Create the WireGuard configuration at `/etc/wireguard/wg0.conf`:

```ini
[Interface]
PrivateKey = <SERVER_PRIVATE_KEY>
Address = 10.8.0.1/24
ListenPort = 51820

# kiosk 1
[Peer]
PublicKey = <KIOSK_PUBLIC_KEY>
AllowedIPs = 10.8.0.101/32

# kiosk 2
[Peer]
PublicKey = <KIOSK_PUBLIC_KEY>
AllowedIPs = 10.8.0.102/32
```

### Step 3: Start WireGuard

```bash
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0
```

## Post-Setup

### Adding Additional Clients

After adding additional clients to the config, restart the VPN using:

```bash
sudo wg-quick down wg0
sudo wg-quick up wg0
```
