

<img width="1292" height="507" alt="image" src="https://github.com/user-attachments/assets/761a1009-c005-42e8-9b8c-d0638c1605f4" />



# RL-Swarm Node Setup Guide (Linux/macOS) — Installation & Error Resolution


Recommended Machine Specs

## · CPU: 8 cores or higher

## · RAM: 32 GB

## · Gpu requirement:

## · GPU VRAM: 16 GB minimum, 24 GB recommended



## 1. System Preparation

## Open Your Vps

```bash
ssh username@ip
```

Linux / WSL

Install the essential packages:

```bash
sudo apt update
sudo apt install -y python3 python3-pip python3-venv git curl wget screen lsof
```

· macOS

```bash
brew install python
```

## ⚙️ 2. Install Node.js, npm & Yarn

Linux / WSL

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - && sudo apt update && sudo apt install -y nodejs
```

## Install Yarn

```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo tee /usr/share/keyrings/yarn.asc >/dev/null
echo "deb [signed-by=/usr/share/keyrings/yarn.asc] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update
sudo apt install -y yarn
```


# macOS Install Yarn

```bash
brew install node
corepack enable
npm i -g yarn
```



# 🧵 4. Running the RL-Swarm Node

1. Create a Screen Session

```bash
screen -S gensyn-node
```


2. Clone the Repository

```bash
git clone https://github.com/gensyn-ai/rl-swarm.git
```


3. Move Into the Folder

```bash
cd rl-swarm
```

4. Create & Activate a Virtual Environment

```bash
python3 -m venv .env
source .env/bin/activate
```

5. Start the Swarm Worker

```bash
bash run_rl_swarm.sh
```


# Now main thing Accessing the Local Signin Dashboard (Port 3000) 

## PRESS   CTRL+A+D   ,     It will dettached your screen :


# NOTE: 
## imp its best practice to open new terminal and login your Vps then run this below cmd's

# Use Cloudflare Tunnel.

## Allow firewall traffic

```bash
sudo apt install -y ufw
sudo ufw allow 22
sudo ufw allow 3000/tcp
sudo ufw enable
```

## Expose Port 3000 via Cloudflare Tunnel (Recommended)


Install Cloudflared

```bash
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
```

Run the tunnel

```bash
cloudflared tunnel --url http://localhost:3000
```

Reattach:

```bash
screen -r gensyn-node
```


When prompted:

Push trained models to HF? → N

Model name? → press Enter for default



# ❗ Troubleshooting Common RL-Swarm Errors

## If you encounter similar errors as shown in the screenshots, use the following solutions. Each fix is mapped to the exact issue it resolves.



## Fix #1 — Node.js Engine Mismatch / Yarn Install Failure

If your logs show messages like:

· The engine "node" is incompatible with this module

· Expected version ">=14". Got "12.x"

· Yarn failing to install dependencies

<img width="1417" height="425" alt="image" src="https://github.com/user-attachments/assets/cc04dc3d-43d9-407e-8dde-89db204be2d6" />


# Then install Node.js using NVM and rerun the worker:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash && \
export NVM_DIR="$HOME/.nvm" && \
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" && \
nvm install 20.19.0 && \
nvm alias default 20.19.0 && \
nvm use default && \
bash run_rl_swarm.sh
```


## Fix #2 — Low RAM / CPU Causing Training Crash

If your VPS has low resources and you see training termination errors,
reduce the training load:

press "ctrl + c"

## Then run this 

```bash
sed -i -E 's/(num_train_samples:\s*)2/\1 1/' rgym_exp/config/rg-swarm.yaml && \
./run_rl_swarm.sh
```

Use this if your system is underpowered, or training fails after a few seconds.

# 🔧 Fix #3 — Hivemind / P2P Daemon Failing to Start


## If your log shows:

<img width="1623" height="297" alt="image" src="https://github.com/user-attachments/assets/f506ec42-79f1-45d2-9e40-49b4a5f5d1f8" />

· Daemon failed to start

· failed to connect to bootstrap peers

· Errors related to /tmp/hivemind-*

Then clear old sockets & restart the P2P daemon:


ctrl + c

```bash
rm -rf /tmp/hivemind-* && export P2P_CONTROL_PATH="/tmp/hivemind-p2pd-new.sock" && pkill -f rl-swarm || true && bash run_rl_swarm.sh
```


✔ This resolves the error shown in the second screenshot ("failed to connect to bootstrap peers").
























