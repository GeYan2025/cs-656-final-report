# Step-by-Step Guidance

## Basic Environment

Ubuntu 22.04 x64 && Windows 11

Author's GitHub Repository: [View on GitHub](https://github.com/kosekmi/2022-imc-dns-over-quic-web-performance)

---

## Reproduce DNS Query Time

### Step 1. Install Dependency Packages

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl build-essential python3-pip unzip
```

### Step 2. Install Go 1.16.15

```bash
wget https://go.dev/dl/go1.16.15.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.16.15.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc
```

### Step 3. Compile Modified DNSPerf

```bash
go build -o dnsperf main.go  # Linux
go build -o dnsperf.exe main.go  # Windows
```

### Step 4. Optimizing Kernel Buffers

```bash
sudo sysctl -w net.core.rmem_max=7500000
sudo sysctl -w net.core.wmem_max=7500000
```

### Step 5. Run DNSPerf Measurement

```bash
./dnsperf         # Linux
./dnsperf.exe     # Windows
```

### Step 6: Run Data Analysis

• **Copy DNSPerf Measurement Results**

   After completing Step 5, locate the `dnsperf_results.csv.gz` file.  
   Copy this file to the following directory (Windows example):

   ```text
   2022-imc-dns-over-quic-web-performance-main\single.query.response.times
   ```

• **Launch Jupyter Notebook**

   Open **Anaconda Navigator** and launch **Jupyter Notebook**.

• **Open Data Analysis Script**

   Navigate to the following path and open the notebook file in Jupyter Notebook:

   ```text
   2022-imc-dns-over-quic-web-performance-main/single.query.response.times.ipynb
   ```

• **Run the Notebook**

   Execute the cells in Jupyter Notebook to analyze the DNS query data.
   The analysis will generate visualizations and statistics based on the DNSPerf results.

---

## Reproduce Web Performance

### Step 1: Set System Timezone Based on VPS Location

```bash
sudo timedatectl set-timezone America/Los_Angeles
timedatectl
```

### Step 2: Install Dependency Packages

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget unzip git build-essential python3 python3-pip
pip3 install pandas
pip3 install pyarrow
pip3 install selenium
```

### Step 3: Install Chromium Browser and Driver

• **Install Chromium Browser and Record Version Number**

```bash
sudo apt install -y chromium-browser
chromium-browser --version
```

• **Install Driver Corresponding to Chromium Browser Version Number**

```bash
wget https://storage.googleapis.com/chrome-for-testing-public/138.0.7204.157/linux64/chromedriver-linux64.zip
unzip chromedriver-linux64.zip
sudo mv chromedriver-linux64/chromedriver /usr/local/bin/
sudo chmod +x /usr/local/bin/chromedriver
# Check the driver version number and makesure the Chromium browser version number must be the same
chromedriver --version
```

---

### Step 4: Install Go 1.18.1

```bash
wget https://go.dev/dl/go1.18.1.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.18.1.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc
```

### Step 5: Clone and Build AdguardTeam/DNSProxy (Pull PR 268)

```bash
git clone https://github.com/AdguardTeam/dnsproxy.git
cd dnsproxy
git fetch origin pull/268/head:pr-268
git checkout pr-268
make build
sudo mv dnsproxy /usr/local/bin/dnsproxy
sudo chmod +x /usr/local/bin/dnsproxy
```

### Step 6: Install Dig Command to Verify DNSProxy

• **Start DNSProxy in Current Session**

```bash
sudo apt install dnsutils -y
sudo sysctl -w net.core.rmem_max=7500000
sudo sysctl -w net.core.wmem_max=7500000
dnsproxy --listen=127.0.0.1 --port=53 --upstream="https://1.1.1.1/dns-query"
```

• **Open New Session to Verify DNSProxy**

```bash
dig @127.0.0.1 -p 53 google.com
sudo pkill dnsproxy
```

### Step 7: Disable System DNS Cache and Set DNSProxy as System Proxy

```bash
systemctl is-active systemd-resolved
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo nano /etc/resolv.conf
# Comment original nameserver and add:
nameserver 127.0.0.1
# After the experiment, remember to restore the system DNS cache and the nameserver in /etc/resolv.conf
```

### Step 8. Run web_performance.py

```bash
sudo sysctl -w net.core.rmem_max=7500000
sudo sysctl -w net.core.wmem_max=7500000
python3 web_performance.py
```

### Step 9: Run Data Analysis

• **Copy Web Performance Results**

   After completing Step 8, locate the `web.performance.parquet.gzip` file.  
   Copy this file to the following directory (Windows example):

   ```text
   2022-imc-dns-over-quic-web-performance-main\web.performance
   ```

• **Launch Jupyter Notebook**

   Open **Anaconda Navigator** and launch **Jupyter Notebook**.

• **Open Data Analysis Script**

   Navigate to the following path and open the notebook file in Jupyter Notebook:

   ```text
   2022-imc-dns-over-quic-web-performance-main/web.performance.ipynb
   ```

• **Run the Notebook**

   Execute the cells in Jupyter Notebook to analyze the web performance data.
   The analysis will generate visualizations and statistics based on the web_performance.py results.

### Step 10: Restore System DNS Cache and Nameserver

```bash
sudo nano /etc/resolv.conf
# Uncomment original nameserver
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved
systemctl status systemd-resolved
```
