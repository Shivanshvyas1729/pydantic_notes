Here is your complete, structured notes guide compiling everything we’ve covered in this session.

---

# WSL & Ubuntu Setup, Recovery, and Reference Guide

## 1. WSL & Ubuntu Installation

*Run these commands in **Windows PowerShell** or **Command Prompt** (as Administrator):*

```powershell
# Install WSL and download default Ubuntu distribution
wsl --install -d Ubuntu

# Check installed WSL distributions and their status
wsl -l -v

# Set Ubuntu as your default WSL distribution
wsl --set-default Ubuntu

```

---

## 2. Linux Environment Setup

*Run these commands inside your **Ubuntu WSL Terminal**:*

### System Update & Upgrade

```bash
# Update package lists and upgrade system packages
sudo apt update && sudo apt upgrade -y

```

### Essential Tools & Dependencies

```bash
# Install base tools (Git, Curl, Build Essentials, CA Certificates)
sudo apt install -y git curl build-essential ca-certificates

# Install Python 3.12 & Virtual Environment support
sudo apt install -y python3.12 python3.12-venv

# Install Node.js & npm
sudo apt install -y nodejs

# Install uv (Fast Python package manager)
curl -LsSf https://astral.sh/uv/install.sh | sh

```

---

## 3. Docker Integration

### Option A: Docker Desktop (Recommended for WSL 2)

1. Open **Docker Desktop** in Windows.
2. Go to **Settings (Gear Icon)** $\rightarrow$ **General**.
3. Check **"Use the WSL 2 based engine"**.
4. Go to **Settings** $\rightarrow$ **Resources** $\rightarrow$ **WSL integration**.
5. Enable the toggle next to **Ubuntu**.
6. Click **Apply & restart**.

### Option B: Native Docker Engine (Inside Ubuntu)

```bash
# 1. Add Docker official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 2. Add repository to Apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 3. Install Docker Engine
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 4. Run Docker without 'sudo'
sudo usermod -aG docker $USER

```

---

## 4. Environment Verification

```bash
# Verify installations
uv --version
node -v
docker --version
docker run hello-world

```

---

## 5. Account & Password Recovery

If you ever forget your WSL username or password:

1. Open **Windows PowerShell**:
```powershell
wsl -d Ubuntu -u root

```


2. Find your username:
```bash
cat /etc/passwd | grep 1000

```


3. Reset your password:
```bash
passwd <your_username>
exit

```


4. Restart WSL from PowerShell:
```powershell
wsl --shutdown

```



---

## 6. Useful WSL Commands & File Access

| Action | Command / Path |
| --- | --- |
| **Check Current Directory** | `pwd` |
| **Convert Path to Windows Format** | `wslpath -w $(pwd)` |
| **Delete File/Folder Recursively** | `rm -rf folder_name` |
| **Access Linux files from Windows** | Type `\\wsl$` in File Explorer address bar |
| **Access Windows files from WSL** | Navigate to `/mnt/c/Users/<Windows_User>/` |
