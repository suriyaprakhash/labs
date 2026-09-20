# HomeLab Gateway: Debian 12 to Local Machine Bridge

# Pre-requisite

```
1. Create the user
Switch to root (su -) if you aren't already, then run:

# Replace 'suriya' with your preferred username
adduser suriya

2. Install Sudo (Debian doesn't always include it)
apt update && apt install sudo

3. Check the existing sudo-ers
getent group sudo

4. Add your user to the Sudo group
usermod -aG sudo suriya

5. Switch to your new user
su - suriya

6. Id username and display info
id suriya
```


# Phase 1: Resource Optimization (Swap)
Debian handles swap similarly, but it is critical for 512MB RAM instances to avoid "Out of Memory" (OOM) errors during apt upgrades or Certbot runs.

```
# 1. Create a 2GB swap file (1M * 2048 = 2GB)
sudo dd if=/dev/zero of=/swapfile bs=1M count=2048

# 2. Secure the file (only root should read/write it)
sudo chmod 600 /swapfile

# 3. Set up the swap area
sudo mkswap /swapfile

# 4. Enable the swap
sudo swapon /swapfile

# 5. Verify the new size
free -h
```