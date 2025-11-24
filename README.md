
# Netrix - Advanced Reverse Tunneling Solution

<div dir="rtl">

# Netrix - راه‌حل پیشرفته تونل معکوس

</div>

[![Go Version](https://img.shields.io/badge/Go-1.20+-00ADD8?style=flat&logo=go)](https://golang.org/)
[![License](https://img.shields.io/badge/License-Commercial-blue.svg)](LICENSE)
[![Release](https://img.shields.io/badge/Release-Stable-green.svg)](https://github.com/yourusername/netrix/releases)

---

<div dir="rtl">

## 🔍 درباره تونل معکوس Netrix

**Netrix** یک راه‌حل پیشرفته و حرفه‌ای برای تونل‌سازی معکوس (Reverse Tunneling) است که برای عبور از NAT، فایروال‌ها و محدودیت‌های شبکه طراحی شده است.

### تونل معکوس چیست؟

تونل معکوس (Reverse Tunneling) یک تکنیک شبکه است که به شما اجازه می‌دهد تا از یک شبکه محدود (مثل شبکه خانگی یا شرکتی با NAT و فایروال) به یک سرور خارجی متصل شوید و سپس از آن سرور برای اتصال به خدمات محلی استفاده کنید.

### معماری Netrix

Netrix از معماری چند لایه استفاده می‌کند:

1. **لایه Transport**: TCP، KCP، WebSocket، Secure WebSocket
   - این لایه اتصال پایه بین کلاینت و سرور را برقرار می‌کند
   - هر پروتکل مزایای خاص خودش را دارد:
     - **TCP**: قابل اعتماد و پایدار، مناسب برای اکثر کاربردها
     - **KCP**: سریع و کم latency، مناسب برای gaming و live streaming
     - **WebSocket**: عبور از فایروال‌های HTTP-aware
     - **WSS**: امن با TLS/SSL

2. **لایه SMUX (Stream Multiplexing)**:
   - چندین stream روی یک transport connection
   - کاهش overhead و استفاده بهینه از connection
   - امکان اجرای همزمان چندین اتصال روی یک tunnel

3. **لایه Session Manager**:
   - مدیریت pool از sessions
   - Load balancing هوشمند (least-loaded selection)
   - Tracking دقیق streams

4. **Frame Protocol برای UDP**:
   - Encapsulation UDP packets داخل frames
   - امکان عبور UDP از طریق tunnel
   - مدیریت چندین UDP flow

### مزایای Netrix

- ✅ **عبور از NAT**: بدون نیاز به تنظیمات router یا port forwarding
- ✅ **عبور از فایروال**: از طریق TCP/WebSocket که معمولاً باز هستند
- ✅ **امنیت**: PSK authentication و TLS encryption
- ✅ **Performance**: بهینه‌سازی شده برای اتصالات زیاد
- ✅ **Multiplexing**: چندین connection روی یک tunnel
- ✅ **UDP Support**: پشتیبانی کامل UDP با frame protocol
- ✅ **Monitoring**: آمار لحظه‌ای و heartbeat monitoring

### کاربردها

- 🎮 **Gaming**: اتصال به game servers از پشت NAT
- 🖥️ **Remote Access**: دسترسی از راه دور به services محلی
- 📡 **Service Exposure**: در دسترس قرار دادن services محلی در اینترنت
- 🔒 **Bypass Restrictions**: عبور از محدودیت‌های شبکه
- 🌐 **VPN Alternative**: جایگزین برای VPN سنتی

</div>

## 🔍 About Netrix Reverse Tunneling

**Netrix** is an advanced and professional reverse tunneling solution designed for NAT traversal, firewall bypass, and network restrictions.

### What is Reverse Tunneling?

Reverse tunneling is a network technique that allows you to connect from a restricted network (like home or corporate network with NAT and firewall) to an external server, then use that server to access local services.

### Netrix Architecture

Netrix uses a multi-layer architecture:

1. **Transport Layer**: TCP, KCP, WebSocket, Secure WebSocket
2. **SMUX Layer**: Stream Multiplexing over a single transport connection
3. **Session Manager**: Pool management and intelligent load balancing
4. **Frame Protocol**: UDP packet encapsulation for tunnel traversal

---

## ⚙️ Configuration Options | تنظیمات

### YAML Configuration | فایل کانفیگ YAML

Netrix supports YAML configuration files for advanced setups. Here are all available options:

<div dir="rtl">

Netrix از فایل‌های کانفیگ YAML پشتیبانی می‌کند. در ادامه تمام تنظیمات موجود:

</div>

#### Basic Settings | تنظیمات پایه

```yaml
# Mode: "server" or "client"
mode: "server"

# Pre-shared key for authentication (required)
psk: "your_secret_key"

# Performance profile: balanced, aggressive, latency, cpu-efficient
profile: "balanced"
```

#### Server Settings | تنظیمات سرور

```yaml
# Server listen address
listen: "0.0.0.0:4000"

# Default transport: tcpmux, kcpmux, wsmux, wssmux
transport: "tcpmux"

# Port mappings (what ports to expose)
maps:
  - type: "tcp"              # tcp or udp
    bind: "0.0.0.0:2066"     # Port to listen on
    target: "127.0.0.1:22"   # Target service

# Maximum concurrent sessions (0 = unlimited)
max_sessions: 0

# TLS certificate files (for wssmux)
cert_file: "/path/to/cert.pem"
key_file: "/path/to/key.pem"
```

#### Client Settings | تنظیمات کلاینت

```yaml
# Client paths (multiple paths supported)
paths:
  - transport: "tcpmux"           # Transport type
    addr: "SERVER_IP:4000"        # Server address
    parallel: 4                    # Legacy: parallel connections
    connection_pool: 4             # Number of simultaneous tunnels
    aggressive_pool: false         # Aggressively reconnect
    retry_interval: 3              # Retry interval in seconds
    dial_timeout: 10               # Connection timeout in seconds
    priority: 1                    # Path priority (optional)
```

#### SMUX Settings | تنظیمات SMUX

```yaml
smux:
  keepalive: 8                     # Keep-alive interval (seconds)
  max_recv: 8388608                # Max receive buffer (bytes, default: 8MB)
  max_stream: 8388608              # Max stream buffer (bytes, default: 8MB)
  frame_size: 32768               # Frame size (bytes, default: 32KB)
```

#### KCP Settings | تنظیمات KCP

```yaml
kcp:
  nodelay: 1                       # Enable nodelay (0=disable, 1=enable)
  interval: 10                     # Update interval (milliseconds)
  resend: 2                        # Fast resend threshold
  nc: 1                            # Disable congestion control (0=disable, 1=enable)
  sndwnd: 768                      # Send window size
  rcvwnd: 768                      # Receive window size
  mtu: 1400                        # Maximum Transmission Unit
```

#### Advanced Settings | تنظیمات پیشرفته

```yaml
advanced:
  # TCP settings
  tcp_nodelay: true                # Enable TCP_NODELAY
  tcp_keepalive: 15                # TCP keep-alive interval (seconds)
  tcp_read_buffer: 4194304         # TCP read buffer (bytes, default: 4MB)
  tcp_write_buffer: 4194304        # TCP write buffer (bytes, default: 4MB)
  
  # UDP settings
  udp_read_buffer: 4194304         # UDP read buffer (bytes, default: 4MB)
  udp_write_buffer: 4194304        # UDP write buffer (bytes, default: 4MB)
  
  # WebSocket settings
  websocket_read_buffer: 262144    # WebSocket read buffer (bytes, default: 256KB)
  websocket_write_buffer: 262144   # WebSocket write buffer (bytes, default: 256KB)
  websocket_compression: false     # Enable WebSocket compression
  
  # Connection management
  cleanup_interval: 3              # Cleanup interval (seconds)
  session_timeout: 30              # Session timeout (seconds)
  connection_timeout: 60           # Connection timeout (seconds)
  stream_timeout: 120              # Stream timeout (seconds)
  max_connections: 2000            # Maximum concurrent connections
  
  # UDP flow management
  max_udp_flows: 1000              # Maximum concurrent UDP flows
  udp_flow_timeout: 300            # UDP flow idle timeout (seconds)
```

#### Logging & Monitoring | لاگ و نظارت

```yaml
# Heartbeat interval (seconds)
heartbeat: 10

# Enable verbose logging
verbose: false
```

### CLI Flags | فلگ‌های خط فرمان

#### Server Flags | فلگ‌های سرور

```bash
netrix server [OPTIONS]

Basic Options:
  -listen string        # Tunnel listen address (default: ":4000")
  -transport string     # Transport: tcpmux|kcpmux|wsmux|wssmux (default: "tcpmux")
  -map string           # Maps: "tcp::bind->target,udp::bind->target"
  -psk string           # Pre-shared key (required)
  -profile string       # Profile: balanced|aggressive|latency|cpu-efficient (default: "balanced")
  -verbose              # Enable verbose logging
  -cert string          # TLS certificate file path (for wssmux)
  -key string           # TLS private key file path (for wssmux)

SMUX Options:
  -smux-keepalive int   # SMUX keepalive interval (seconds, override profile)
  -smux-max-recv int    # SMUX max receive buffer (bytes, override profile)
  -smux-max-stream int  # SMUX max stream buffer (bytes, override profile)
  -smux-frame-size int  # SMUX frame size (bytes, override profile, default: 32768)

KCP Options:
  -kcp-nodelay int      # KCP nodelay (override profile: 0=disable, 1=enable)
  -kcp-interval int     # KCP interval (milliseconds, override profile)
  -kcp-resend int       # KCP resend threshold (override profile)
  -kcp-nc int           # KCP nc - disable congestion control (override profile: 0=disable, 1=enable)
  -kcp-sndwnd int       # KCP send window (override profile)
  -kcp-rcvwnd int       # KCP receive window (override profile)
  -kcp-mtu int          # KCP MTU (override profile)
```

#### Client Flags | فلگ‌های کلاینت

```bash
netrix client [OPTIONS]

Basic Options:
  -server string        # Server address host:port (legacy single-path mode)
  -transport string     # Transport: tcpmux|kcpmux|wsmux|wssmux (default: "tcpmux")
  -paths string         # Multi-path: "tcpmux:addr:parallel,kcpmux:addr:parallel,..."
  -psk string           # Pre-shared key (required)
  -profile string       # Profile: balanced|aggressive|latency|cpu-efficient (default: "balanced")
  -verbose              # Enable verbose logging

Connection Pool Options:
  -parallel int         # Number of parallel tunnels (legacy, default: 1)
  -connection-pool int  # Number of simultaneous tunnels (alias of parallel, default: 0)
  -aggressive-pool      # Aggressively re-dial tunnels to minimize downtime
  -retry-interval duration  # Retry interval for dial errors (default: 3s)
  -dial-timeout duration    # Dial timeout for tunnel transports (default: 10s)

SMUX Options: (same as server)
  -smux-keepalive int
  -smux-max-recv int
  -smux-max-stream int
  -smux-frame-size int

KCP Options: (same as server)
  -kcp-nodelay int
  -kcp-interval int
  -kcp-resend int
  -kcp-nc int
  -kcp-sndwnd int
  -kcp-rcvwnd int
  -kcp-mtu int
```

---

## 📋 Transport Protocol Examples | نمونه‌های پروتکل‌های انتقال

### 1. TCP Multiplexing (tcpmux)

<div dir="rtl">

**TCP Multiplexing** از پروتکل TCP استاندارد با SMUX استفاده می‌کند. مناسب برای اکثر کاربردها با reliability بالا.

</div>

**TCP Multiplexing** uses standard TCP protocol with SMUX. Suitable for most use cases with high reliability.

#### Server Configuration File | فایل کانفیگ سرور

Create `server-tcp.yaml`:

```yaml
mode: "server"
listen: "0.0.0.0:4000"
transport: "tcpmux"
psk: "your_secret_key_here"
profile: "balanced"

maps:
  - type: "tcp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:22"
  - type: "udp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:2066"

heartbeat: 10
verbose: false
```

Run server:
```bash
netrix -config server-tcp.yaml
```

#### Client Configuration File | فایل کانفیگ کلاینت

Create `client-tcp.yaml`:

```yaml
mode: "client"
psk: "your_secret_key_here"
profile: "balanced"

paths:
  - transport: "tcpmux"
    addr: "SERVER_IP:4000"
    connection_pool: 4
    aggressive_pool: false
    retry_interval: 3
    dial_timeout: 10

heartbeat: 10
verbose: false
```

Run client:
```bash
netrix -config client-tcp.yaml
```

---

### 2. KCP Multiplexing (kcpmux)

<div dir="rtl">

**KCP Multiplexing** از پروتکل KCP (UDP-based) با SMUX استفاده می‌کند. مناسب برای gaming، live streaming و کاربردهای حساس به latency.

</div>

**KCP Multiplexing** uses KCP protocol (UDP-based) with SMUX. Ideal for gaming, live streaming, and latency-sensitive applications.

#### Server Configuration File | فایل کانفیگ سرور

Create `server-kcp.yaml`:

```yaml
mode: "server"
listen: "0.0.0.0:4001"
transport: "kcpmux"
psk: "your_secret_key_here"
profile: "latency"

maps:
  - type: "tcp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:22"
  - type: "udp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:2066"

kcp:
  nodelay: 1
  interval: 8
  resend: 2
  nc: 1
  sndwnd: 768
  rcvwnd: 768
  mtu: 1350

heartbeat: 10
verbose: false
```

Run server:
```bash
netrix -config server-kcp.yaml
```

#### Client Configuration File | فایل کانفیگ کلاینت

Create `client-kcp.yaml`:

```yaml
mode: "client"
psk: "your_secret_key_here"
profile: "latency"

paths:
  - transport: "kcpmux"
    addr: "SERVER_IP:4001"
    connection_pool: 4
    aggressive_pool: true
    retry_interval: 1
    dial_timeout: 5

kcp:
  nodelay: 1
  interval: 8
  resend: 2
  nc: 1
  sndwnd: 512
  rcvwnd: 512
  mtu: 1350

heartbeat: 10
verbose: false
```

Run client:
```bash
netrix -config client-kcp.yaml
```

---

### 3. WebSocket Multiplexing (wsmux)

<div dir="rtl">

**WebSocket Multiplexing** از پروتکل WebSocket با SMUX استفاده می‌کند. مناسب برای عبور از فایروال‌های HTTP-aware و پروکسی‌ها.

</div>

**WebSocket Multiplexing** uses WebSocket protocol with SMUX. Ideal for bypassing HTTP-aware firewalls and proxies.

#### Server Configuration File | فایل کانفیگ سرور

Create `server-ws.yaml`:

```yaml
mode: "server"
listen: "0.0.0.0:8080"
transport: "wsmux"
psk: "your_secret_key_here"
profile: "balanced"

maps:
  - type: "tcp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:22"
  - type: "udp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:2066"

advanced:
  websocket_read_buffer: 262144
  websocket_write_buffer: 262144
  websocket_compression: false

heartbeat: 10
verbose: false
```

Run server:
```bash
netrix -config server-ws.yaml
```

#### Client Configuration File | فایل کانفیگ کلاینت

Create `client-ws.yaml`:

```yaml
mode: "client"
psk: "your_secret_key_here"
profile: "balanced"

paths:
  - transport: "wsmux"
    addr: "SERVER_IP:8080"
    connection_pool: 8
    aggressive_pool: false
    retry_interval: 3
    dial_timeout: 10

advanced:
  websocket_read_buffer: 262144
  websocket_write_buffer: 262144
  websocket_compression: false

heartbeat: 10
verbose: false
```

Run client:
```bash
netrix -config client-ws.yaml
```

---

### 4. Secure WebSocket Multiplexing (wssmux)

<div dir="rtl">

**Secure WebSocket Multiplexing** از WebSocket با TLS/SSL استفاده می‌کند. مناسب برای اتصالات امن و عبور از فایروال‌ها.

</div>

**Secure WebSocket Multiplexing** uses WebSocket with TLS/SSL encryption. Ideal for secure connections and firewall bypass.

#### Generate TLS Certificate | تولید گواهینامه TLS

```bash
# Generate private key
openssl genpkey -algorithm RSA -out server.key -pkeyopt rsa_keygen_bits:2048

# Generate certificate signing request
openssl req -new -key server.key -out server.csr

# Generate self-signed certificate (valid for 365 days)
openssl x509 -req -in server.csr -signkey server.key -out server.crt -days 365
```

#### Server Configuration File | فایل کانفیگ سرور

Create `server-wss.yaml`:

```yaml
mode: "server"
listen: "0.0.0.0:8443"
transport: "wssmux"
psk: "your_secret_key_here"
profile: "balanced"

cert_file: "/path/to/server.crt"
key_file: "/path/to/server.key"

maps:
  - type: "tcp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:22"
  - type: "udp"
    bind: "0.0.0.0:2066"
    target: "127.0.0.1:2066"

advanced:
  websocket_read_buffer: 262144
  websocket_write_buffer: 262144
  websocket_compression: false

heartbeat: 10
verbose: false
```

Run server:
```bash
netrix -config server-wss.yaml
```

#### Client Configuration File | فایل کانفیگ کلاینت

Create `client-wss.yaml`:

```yaml
mode: "client"
psk: "your_secret_key_here"
profile: "balanced"

paths:
  - transport: "wssmux"
    addr: "SERVER_IP:8443"
    connection_pool: 8
    aggressive_pool: false
    retry_interval: 3
    dial_timeout: 10

advanced:
  websocket_read_buffer: 262144
  websocket_write_buffer: 262144
  websocket_compression: false

heartbeat: 10
verbose: false
```

Run client:
```bash
netrix -config client-wss.yaml
```

---

## 🚀 Quick Start | شروع سریع

### Installation | نصب

```bash
git clone https://github.com/yourusername/netrix.git
cd netrix
go build -o netrix
```

### Basic Usage | استفاده پایه

#### Server (TCP):
```bash
netrix server -listen 0.0.0.0:4000 -transport tcpmux -psk your_secret_key -map "tcp::0.0.0.0:2066->127.0.0.1:22"
```

#### Client (TCP):
```bash
netrix client -server SERVER_IP:4000 -transport tcpmux -parallel 4 -psk your_secret_key
```

---

## 📄 License | مجوز

This project is commercial software. Please contact the author for licensing information.

<div dir="rtl">

این پروژه نرم‌افزار تجاری است. لطفاً برای اطلاعات مجوز با نویسنده تماس بگیرید.

</div>

---

Made with ❤️ by Netrix Team
