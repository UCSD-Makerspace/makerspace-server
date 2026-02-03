# Setup

## Prerequisites

- Ansible installed locally
- kubectl installed locally
- Helmfile installed locally

## Instructions

### Step 1: Setup WireGuard

- **Server:** See [setup-wireguard-server.md](setup-wireguard-server.md)
- **Kiosk:** See [setup-wireguard-kiosk.md](setup-wireguard-kiosk.md)

### Step 2: Create Secret

Copy the following file:
* `inventory.example.ini` → `inventory.ini`

Fill out the needed information about the setup.

### Step 3: Cluster Setup

Test if ansible can connect to the servers (ssh into each vps before to verify their host keys):

```bash
ansible -i inventory.ini all -m ping
```

Run the playbook to set up K3s:

```bash
ansible-playbook -i env/inventory.ini ansible/setup-k3s.yml
```

### Step 4: Configure Local kubectl Access
Setup local access to use `kubectl`. The following script can serve as a helper to do this faster, but this can always be done manually (the script is a bit scuffed).

```bash
./scripts/setup-kubeconfig.sh USER@VPS_IP CONTEXT_NAME 6443
```

### Step 5: Create Secrets

Create the required secrets in the cluster before deploying:

```bash
kubectl create secret generic env-vars \
  --namespace ciapi \
  --from-literal=UCSD_CLIENT_ID='<your-client-id>' \
  --from-literal=UCSD_CLIENT_SECRET='<your-client-secret>' \
  --from-literal=FABMAN_API_TOKEN='<your-fabman-token>' \
  --from-literal=USER_DB_ID='<spreadsheet-id>' \
  --from-literal=WAIVER_DB_ID='<spreadsheet-id>' \
  --from-literal=ACTIVITY_SHEET_ID='<spreadsheet-id>'
```

```bash
kubectl create secret generic google-credentials \
  --namespace ciapi \
  --from-file=creds.json=/path/to/creds.json
```


### Step 6: Install dependencies

```bash
helmfile sync
```

If the machine still uses a micro sd this might take quite a while it can be helpful to take a look at what's going on with:
```bash
watch -d kubectl get pods -A
```

