# Step-by-Step Guidance

## Basic Environment

Ubuntu 22.04 x64 && Windows 11

---

## Reproduce DNS Query Time

### Step 1. Install dependency packages

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
   Navigate to the following path and open the notebook file:

   ```text
   2022-imc-dns-over-quic-web-performance-main/single.query.response.times.ipynb
   ```

• **Run the Notebook**  
   Execute the cells in the notebook to analyze the DNS performance data.
   The analysis will generate visualizations and statistics based on the DNSPerf results.

---

## Reproduce Web Performance

## ⏰ 设置时区

```bash
sudo timedatectl set-timezone America/Los_Angeles
timedatectl
```

## 🧰 安装依赖包

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget unzip git build-essential python3 python3-pip
pip3 install pandas
pip3 install pyarrow
pip3 install selenium
```

## 🧪 安装浏览器与驱动

```bash
sudo apt install -y chromium-browser
chromium-browser --version
```

```bash
wget https://storage.googleapis.com/chrome-for-testing-public/138.0.7204.157/linux64/chromedriver-linux64.zip
unzip chromedriver-linux64.zip
sudo mv chromedriver-linux64/chromedriver /usr/local/bin/
sudo chmod +x /usr/local/bin/chromedriver
chromedriver --version
```

---

## 🧱 安装 Go 1.18.1（用于 dnsproxy）

```bash
wget https://go.dev/dl/go1.18.1.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.18.1.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc
```

## 🧬 构建 dnsproxy（Pull Request 268）

```bash
git clone https://github.com/AdguardTeam/dnsproxy.git
cd dnsproxy
git fetch origin pull/268/head:pr-268
git checkout pr-268
make build
```

## 🧪 DNS 测试与配置

```bash
sudo apt install dnsutils -y
sudo sysctl -w net.core.rmem_max=7500000
sudo sysctl -w net.core.wmem_max=7500000
./dnsproxy --listen=127.0.0.1 --port=53 --upstream="https://1.1.1.1/dns-query"
dig @127.0.0.1 -p 53 google.com
sudo pkill dnsproxy
```

## ❌ 关闭 systemd-resolved 并修改 resolv.conf

```bash
systemctl is-active systemd-resolved
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo nano /etc/resolv.conf
# 修改为：
nameserver 127.0.0.1
```
