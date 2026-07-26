Check the steps in [Debian Readme](../DEBIAN_README.md) to create **sudo user**, **swap space** and for **tailscale exit node**

## Installing Docker on Debian 13 (Trixie)

Follow these steps to set up the official Docker repository and install the latest Docker Engine on Debian 13.

### 1. Clear Conflicting Packages
Remove any outdated or unofficial Docker installations that might cause system conflicts.
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get purge -y \$pkg; done
```

### 2. Install Required Setup Tools
Update your local software database and install certificates that enable secure communication with the repository.
```bash
sudo apt update
sudo apt install -y ca-certificates curl
```

### 3. Add Docker's Trust Security Key
Download and save Docker's cryptographic GPG key to validate package authenticity.
```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://docker.com -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### 4. Link the Official Docker Repository
Create the repository source file, utilizing the `trixie` (Debian 13) suite designation.
```bash
echo "deb [arch=\$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://docker.com trixie stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 5. Download and Deploy Docker
Update your package lists to include the new repository and install the latest Docker engine components.
```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 6. Verify the Installation
Confirm a successful installation by running the default test image.
```bash
sudo docker run hello-world
```

### 7. Run Commands Without sudo (Optional)
To run Docker commands without prefixing `sudo`, add your user to the `docker` group. Log out and log back in for changes to take effect.
```bash
sudo usermod -aG docker \$USER
```

# Nginx Proxy Manager Deployment Guide

This guide provides everything you need to install, deploy, and fully secure Nginx Proxy Manager over HTTPS using Docker on Debian 13 (Trixie).

---

## 1. Project Directory Setup
Organize your deployment files by creating a dedicated folder for Nginx Proxy Manager.
```bash
mkdir -p ~/nginx-proxy-manager && cd ~/nginx-proxy-manager
```

---

## 2. Configuration File Creation
Create the `docker-compose.yml` file using a terminal text editor.
```bash
nano docker-compose.yml
```

Paste the following configuration inside the file, then save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`):
```yaml
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '443:443'
      - '81:81'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

---

## 3. Initial Boot Up
Launch the stack in detached mode so that it continuously runs silently in the background without needing `sudo`.
```bash
docker compose up -d
```

---

## 4. Initial Administration Login
Open your web browser and navigate to your server's IP address on port `81` to manage your hosts.
* **URL:** `http://<your-server-ip>:81`
* **Default Username:** `admin@example.com`
* **Default Password:** `changeme`

*Note: You will be immediately prompted to change these default credentials upon your very first login.*

---

## 5. Enable SSL on the Subdomain
To ensure your panel is isolated and accessible strictly via its dedicated subdomain (`nginx-proxy-manager.suriyaprakhash.com`) instead of the main root domain (`suriyaprakhash.com`), follow these routing rules.

1. Verify your DNS records route the specific subdomain `nginx-proxy-manager.suriyaprakhash.com` directly to this Debian server.
2. In the Admin Panel, go to **Dashboard** ➔ **Proxy Hosts** ➔ **Add Proxy Host**.
3. Configure the **Details** tab:
   * **Domain Names:** `nginx-proxy-manager.suriyaprakhash.com` *(Do not add the main domain here)*
   * **Scheme:** `http`
   * **Forward Hostname / IP:** `127.0.0.1`
   * **Forward Port:** `81`
   * **Block Common Exploits:** Toggle **On**
4. Switch to the **SSL** tab:
   * **SSL Certificate:** Select **Request a new SSL Certificate**
   * **Force SSL:** Toggle **On**
   * **HTTP/2 Support:** Toggle **On**
   * Agree to the Let's Encrypt Terms of Service.
5. Click **Save**. 

---

## 6. Secure and Close Port 81
Now that the interface is accessible via `https://suriyaprakhash.com`, lock down port `81` so it can no longer be accessed directly from the outside world.

1. Open your configuration file:
   ```bash
   nano ~/nginx-proxy-manager/docker-compose.yml
   ```

2. Modify the `ports` block for port `81` by prefixing it with `127.0.0.1:`. Your file should match this structure:
   ```yaml
   services:
     app:
       image: 'jc21/nginx-proxy-manager:latest'
       restart: unless-stopped
       ports:
         - '80:80'
         - '443:443'
         - '127.0.0.1:81:81' # Disallows external connections to port 81
       volumes:
         - ./data:/data
         - ./letsencrypt:/etc/letsencrypt
   ```

3. Save and close the file (`Ctrl+O`, `Enter`, `Ctrl+X`).

4. Restart your stack to apply the strict firewall rule:
   ```bash
   docker compose up -d --force-recreate
   ```

---

## Verification
You can now strictly access your management interface securely via **`https://suriyaprakhash.com`**. Your root domain remains untouched, and direct external connection attempts to `http://<your-server-ip>:81` will be completely blocked.

