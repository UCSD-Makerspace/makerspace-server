# WireGuard Kiosk Setup

## Prerequisites

- WireGuard installed on the kiosk
- The server's public key and IP address (from [setup-wireguard-server.md](setup-wireguard-server.md))

Install WireGuard with:
```bash
sudo apt update && sudo apt install wireguard
```

## Instructions

### Step 1: Generate Keys

```bash
cd /etc/wireguard
wg genkey | tee privatekey | wg pubkey > publickey
```

### Step 2: Configure VPN

Create the WireGuard configuration at `/etc/wireguard/wg0.conf` where `KIOSK_IP` is `10.8.0.101`,`10.8.0.102`,etc:

```ini
[Interface]
PrivateKey = <KIOSK_PRIVATE_KEY>
Address = KIOSK_IP/24

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
Endpoint = <SERVER_IP_ADDRESS>:51820
AllowedIPs = 10.8.0.1/32
PersistentKeepalive = 25
```

### Step 3: Start WireGuard

Connect:
```bash
sudo wg-quick up wg0
```

### Step 4: Verify Connection

```bash
ping 10.8.0.1
```

You should see successful ping responses.

## Post-Setup

### Access Kubernetes over VPN

Update your kubeconfig to use the VPN IP:

```bash
kubectl config set-cluster <context-name> --server=https://10.8.0.1:6443
```

### Startup on Boot

```bash
sudo systemctl enable wg-quick@wg0
```
