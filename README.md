# Amnezia VPN Docker Server

This is a Docker image for the Amnezia VPN server gateway. It allows you to deploy a secure SSH gateway that the Amnezia VPN client uses to install and manage various VPN protocols (AmneziaWG, XRay, OpenVPN, etc.) inside Docker containers.

## Installation

### 1. Requirements
- Docker and Docker Compose installed on your host machine.
- [Amnezia VPN client](https://amnezia.org/downloads) installed on your local computer.

### 2. Prepare the Repository
```sh
git clone https://github.com/t0ny-m/AmneziaVPNDockerServer.git
cd AmneziaVPNDockerServer
docker compose build
```

### 3. Generate SSH Keys
For the best compatibility with the Amnezia VPN client, it is recommended to use the **ED25519** format (RSA 4096 keys might be too long for some client versions):

```sh
ssh-keygen -t ed25519 -f ./ssh-keys/amz_key -N ""
```
- **Public Key**: `./ssh-keys/amz_key.pub`
- **Private Key**: `./ssh-keys/amz_key` (you will need its content for the Amnezia client)

### 4. Configure Authentication
1. **Manage `authorized_keys`**:
   Copy the content of your public key (`./ssh-keys/amz_key.pub`) into a new file named `authorized_keys` in the root directory.
   Set proper permissions:
   ```bash
   sudo chown root:root authorized_keys
   chmod 600 authorized_keys
   ```
2. **Setup Permissions**:
   ```bash
   sudo chown root:root authorized_keys
   chmod 600 authorized_keys
   ```
3. **Adjust SSH Settings**:
   Ensure `setup/00-standard.conf` is configured to bypass strict mode checks (common issue with Docker volume mounts) and allow password fallback if needed:
   ```conf
   PermitRootLogin yes
   PasswordAuthentication yes
   MaxAuthTries 5
   StrictModes no
   ```

### 5. Start the Container
```sh
docker compose up -d --build
```

### 6. Connect via Amnezia VPN Client
Open the Amnezia VPN client on your computer and choose **Self-hosted VPN**:

- **IP Address**: `your_server_ip:2222`
- **Username**: `root`
- **SSH Private Key**: Paste the entire content of your private key file (`./ssh-keys/amz_key`).

*Note: If key authentication fails, you can use the default password `amnezia_secret_777` (configured in the Dockerfile).*

### 7. Setup VPN Protocols
Once connected, the Amnezia client will display a list of protocols. Choose the one you need and click **Setup**. The client will automatically containerize the chosen protocols on your server.

## Troubleshooting
- **Permission Denied**: Ensure `authorized_keys` is owned by `root:root` and has `600` permissions.
- **Port Forwarding**: Only port `2222` needs to be exposed on the host. Other ports for VPN protocols are handled internally or forwarded by Amnezia during setup.

## License
Licensed under the Apache License.

**Disclaimer**: This project is not affiliated with the Amnezia VPN developers. Use at your own risk.
