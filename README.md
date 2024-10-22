# ZetaChain Node Setup Guide

#### Prerequisites

Make sure you have the following installed on your machine:

1. **Go** - Install the latest version of Go by following the instructions on the [official Go website](https://golang.org/doc/install).
2. **Git** - Make sure Git is installed on your machine. You can check it using:
    ```bash
    git --version
    ```
3. **Docker** - If Docker is required for your specific setup, you can install it from [Docker's official site](https://docs.docker.com/get-docker/).

4. **System Requirements:**
    - **CPU:** At least 2 cores
    - **RAM:** 4GB minimum
    - **Disk Space:** 100GB SSD recommended
    - **OS:** Linux (Ubuntu is recommended) or macOS

#### Step 1: Install Dependencies

If you're using a Linux machine, run the following commands to install the required dependencies.

```bash
sudo apt update
sudo apt install build-essential git curl -y
```

#### Step 2: Install Go

You need Go version 1.19+ installed. Run these commands to install Go:

```bash
wget https://golang.org/dl/go1.19.6.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.19.6.linux-amd64.tar.gz
```

Add Go to your `PATH` by editing your `~/.profile` or `~/.bashrc`:

```bash
echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
source ~/.profile
```

Verify Go is installed:

```bash
go version
```

#### Step 3: Clone the ZetaChain Repository

Use Git to clone the ZetaChain source code:

```bash
git clone https://github.com/zeta-chain/node.git
cd node
```

#### Step 4: Build the ZetaChain Node

Navigate to the ZetaChain directory and build the project:

```bash
make install
```

After this, you should have the `zetacored` binary in your `$GOPATH/bin` directory.

#### Step 5: Configure the Node

Now, initialize the node with a specific name (replace `your_node_name` with your desired node name):

```bash
zetacored init your_node_name --chain-id athens_7001-1
```

This will create the default configuration files in `~/.zetacored`.

#### Step 6: Set Up the Genesis File and Seeds

Download the genesis file:

```bash
curl https://raw.githubusercontent.com/zeta-chain/network-athens_7001-1/main/genesis.json > ~/.zetacored/config/genesis.json
```

Update persistent peers and seeds in the `config.toml` file:

```bash
nano ~/.zetacored/config/config.toml
```

Find the following lines and update them as shown:

```toml
persistent_peers = "<PEERS>"
seeds = "<SEEDS>"
```

Replace `<PEERS>` and `<SEEDS>` with the relevant addresses provided by ZetaChain.

#### Step 7: Set Minimum Gas Prices

Open `app.toml` to set the minimum gas prices. This file is located at `~/.zetacored/config/app.toml`.

Set the gas price:

```toml
minimum-gas-prices = "0.01azeta"
```

#### Step 8: Start the Node

You are now ready to start your node:

```bash
zetacored start
```

The node will start syncing with the blockchain. You can check logs to ensure everything is running smoothly.

#### Step 9: Check Node Status

To verify if your node is up and running, use the following command:

```bash
zetacored status
```

This will show you the synchronization status and other node information.

### Optional: Running the Node in Background with `systemd`

If you want your node to run as a service and restart automatically after reboot, follow these steps:

1. Create a new service file:

```bash
sudo nano /etc/systemd/system/zetacored.service
```

2. Add the following configuration to the file:

```ini
[Unit]
Description=ZetaChain Node
After=network-online.target

[Service]
User=$USER
ExecStart=/usr/local/bin/zetacored start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

3. Reload the system daemon and enable the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable zetacored
```

4. Start the service:

```bash
sudo systemctl start zetacored
```

Now, your node will run in the background and restart automatically.
