Check the steps in [Debian Readme](../DEBIAN_README.md) to create **swap space** and for **tailscale exit node**

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
