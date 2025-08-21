# Project: Secure Virtual Network Link Using WireGuard VPN

## Overview
This project implements a high-performance, secure VPN tunnel between two Linux machines using WireGuard, a modern VPN protocol utilizing state-of-the-art cryptography. The solution creates an encrypted point-to-point connection with minimal overhead while maintaining near-native network performance.

---

## Organizational Application

### Importance to Companies
This WireGuard implementation is crucial for establishing secure, high-performance connections between remote offices and cloud resources. Its modern cryptography and minimal overhead make it ideal for bandwidth-sensitive applications while maintaining robust security for organizational data in transit.

### Use Case Scenario
A software development company uses WireGuard to connect its distributed development teams to centralized code repositories and testing environments, ensuring secure, low-latency access to critical resources without compromising development velocity.

---

## Configuration & Screenshots

### 1. Initial Network Configuration
- Verified baseline network connectivity between LinuxVM1 (10.174.237.20) and LinuxVM2 (10.174.237.40)
- Confirmed Ubuntu 22.04 LTS installation on both systems
- Established prerequisite network connectivity for WireGuard deployment

![Linux Machine IP Configuration](screenshots/linuxvm-ip-configuration.png)
*Initial network configuration showing IP addresses of both Linux systems*

### 2. WireGuard Installation
- Installed WireGuard package on both systems using apt package manager
- Verified kernel module installation and userspace tools availability
- Confirmed successful installation of wg and wg-quick utilities

![WireGuard Installation](screenshots/wireguard-installation.png)
*WireGuard package installation process on Ubuntu 22.04 LTS*

### 3. Cryptographic Key Generation
- Generated unique public/private key pairs for each machine
- Implemented proper file permissions (chmod 600) for private key security
- Established foundation for secure peer authentication

![Key Generation](screenshots/key-generation.png)
*Cryptographic key pair generation using wg genkey command*

### 4. WireGuard Interface Configuration
- Created wg0.conf configuration files with precise network settings
- Configured LinuxVM1 with tunnel IP 10.10.10.1/24
- Configured LinuxVM2 with tunnel IP 10.10.10.2/24
- Implemented PersistentKeepalive for NAT traversal

![Configuration Files](screenshots/wg0-configuration.png)
*WireGuard configuration files showing tunnel IP assignments and peer settings*

### 5. Tunnel Activation and Status Verification
- Activated WireGuard interface using wg-quick up command
- Verified successful peer handshakes and connection establishment
- Monitored tunnel status and encryption statistics

![Tunnel Activation](screenshots/tunnel-activation.png)
*WireGuard tunnel activation and status verification showing active peers*

### 6. Connectivity Testing and Validation
- Conducted comprehensive ping tests between tunnel endpoints
- Verified bidirectional communication through encrypted tunnel
- Tested both tunnel IP addresses and physical network connectivity

![Connectivity Testing](screenshots/connectivity-testing.png)
*Comprehensive connectivity testing through WireGuard tunnel*

### 7. Routing Table Verification
- Examined routing table to confirm proper tunnel route installation
- Verified traffic routing through wg0 interface
- Confirmed correct network path selection

![Routing Table](screenshots/routing-table.png)
*Routing table verification showing WireGuard tunnel routes*

---

## Observations and Challenges

### Initial Implementation Challenges
- **Cryptographic Key Mismatch**: Initial tunnel failure due to public key configuration errors
- **Firewall Port Blocking**: UDP port 51820 blocked by default UFW configuration
- **Routing Conflicts**: Initial CIDR notation errors causing improper traffic routing

### Performance Considerations
- **Minimal CPU Overhead**: WireGuard's efficient cryptographic implementation
- **Low Latency Impact**: Near-native network performance measurements
- **Bandwidth Efficiency**: Minimal protocol overhead compared to traditional VPN solutions

### Security Implementation
- **Perfect Forward Secrecy**: Ephemeral key exchange implementation
- **Cryptographic Best Practices**: Curve25519, ChaCha20, Poly1305, BLAKE2s
- **Minimal Attack Surface**: Compact codebase reducing vulnerability exposure

---

## Lessons Learned

### Technical Implementation Insights
- **Configuration Simplicity**: WireGuard's minimalist configuration approach
- **NAT Traversal Effectiveness**: PersistentKeepalive mechanism reliability
- **Kernel Integration Benefits**: Performance advantages of in-kernel implementation

### Operational Best Practices
- **Key Management**: Secure private key storage and permission management
- **Configuration Validation**: Importance of wg-quick strip for syntax checking
- **Incremental Testing**: Staged deployment and validation methodology

### Security Architecture
- **Cryptographic Modernity**: Advantages of modern cryptographic primitives
- **Minimal Trust Model**: Reduced attack surface through simplicity
- **Performance-Security Balance**: Achieving both high performance and strong security

---

## How to Reproduce

### Prerequisites
- Two Ubuntu 22.04 LTS systems with network connectivity
- Administrative access (sudo privileges) on both machines
- UDP port 51820 open in firewall configurations

### Implementation Steps

1. **Install WireGuard**
```bash
sudo apt update
sudo apt install wireguard resolvconf
```

2. **Generate Cryptographic Keys**
```bash
# Generate private key with secure permissions
wg genkey | sudo tee /etc/wireguard/privatekey | wg pubkey | sudo tee /etc/wireguard/publickey
sudo chmod 600 /etc/wireguard/privatekey
```

3. **Create WireGuard Configuration**
```bash
# LinuxVM1 Configuration (/etc/wireguard/wg0.conf)
[Interface]
PrivateKey = <LINUXVM1_PRIVATE_KEY>
Address = 10.10.10.1/24
ListenPort = 51820

[Peer]
PublicKey = <LINUXVM2_PUBLIC_KEY>
AllowedIPs = 10.10.10.2/32
Endpoint = 10.174.237.40:51820

# LinuxVM2 Configuration (/etc/wireguard/wg0.conf)
[Interface]
PrivateKey = <LINUXVM2_PRIVATE_KEY>
Address = 10.10.10.2/24
ListenPort = 51820
PersistentKeepalive = 25

[Peer]
PublicKey = <LINUXVM1_PUBLIC_KEY>
AllowedIPs = 10.10.10.1/32
Endpoint = 10.174.237.20:51820
```

4 **Enable and Start WireGuard**
```bash
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0
```

5 **Firewall Configuration**
```bash
sudo ufw allow 51820/udp
sudo ufw reload
```

6 **Verification Commands**
```bash
# Check tunnel status
sudo wg show

# Verify interface configuration
ip addr show wg0

# Test connectivity
ping -c 4 10.10.10.2

# Check routing table
ip route list
