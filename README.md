# Proxmox-Private-Network

# Introduction

The Proxmox-Private-Network guide provides a comprehensive step-by-step tutorial for setting up a private network within a Proxmox environment. This process involves configuring a virtual network bridge, modifying network settings through SSH or the Proxmox shell, and ensuring proper NAT settings for network address translation. By following this guide, users can effectively establish a private network to manage VMs with isolated network environments. This can be useful when creating Highly Available Kubernetes clusters that need at least 6 Servers (3 Master and 3 Workers).

# Instruction
#### 1. Create a VM (if it doesn't exist).

#### 2. Create a Virtual Network (e.g., vmbr1)

- Select the node (Proxmox Host) under Datacenter.

- Navigate to the "Network" section in the "System" tab.
  
- Click "Create" and choose "Linux Bridge"

- Enter your network IP (e.g., "192.168.1.1/24") in the IP4/CIDR box.

- Click "Apply Configuration"

![Screenshot 2024-07-01 at 10 42 00](https://github.com/adel-bz/Proxmox-Private-Network/assets/45201934/26af5bf1-dc10-4ef5-90ec-63832f328d06)


### 3. SSH into Proxmox Host
- Use SSH or the "Shell" option on the Proxmox panel to connect to the Proxmox host.
 
### 4. Update Network Interfaces
- Add the following code to ```/etc/network/interfaces``` (adjust network name and IPs as needed):
  
```
auto vmbr1
iface vmbr1 inet static
        address 192.168.1.1
        netmask 255.255.255.0
        bridge-ports none
        bridge-stp off
        bridge-fd 0

# NAT SETTINGS
post-up echo 1 > /proc/sys/net/ipv4/ip_forward
post-up iptables -t nat -A POSTROUTING -s '192.168.1.0/24' -o enp6s0 -j MASQUERADE
post-down iptables -t nat -D POSTROUTING -s '192.168.1.0/24' -o enp6s0 -j MASQUERADE
```

![Screenshot 2024-07-01 at 10 58 17](https://github.com/adel-bz/Proxmox-Private-Network/assets/45201934/86c269d1-e237-4688-91f3-70c3f9487a09)


#### 5. Restart Network:
```
# sudo systemctl restart networking
```

#### 6. Configure VM Network Device
- In the Proxmox panel, select your VM.

- In the "Hardware" section, change the network device to ```vmbr1```.

#### 7. Set IP and Gateway (for Cloud-init)
- Under the Hardware tab, go to the Cloud-Init tab.

- Edit the IP Config option with your specific IP.

![Screenshot 2024-07-01 at 11 13 23](https://github.com/adel-bz/Proxmox-Private-Network/assets/45201934/23b2d626-8e9b-4c70-a45f-f060b9e85b4b)


#### 8. Start VM and Verify Connection
- Start your machine

- check the internet connection with:

```
# Ping 4.2.2.4 
```

For a visual reference, watch this example video (note: it may not follow these exact steps).


