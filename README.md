# Proxmox-Private-Network

1. In the first step, you must create a VM (If it doesn't exist)

2. Create a virtual network (in my case vmbr1)

- Select the node (Proxmox Host) under datacenter then go to the ``` Network ``` section in the ``` System ``` tab.

- Click on the ``` Create ``` button and choose ``` Linux Bridge ``` network then input your IP for your network (in my case "192.168.1.1/24") in the ``` IP4/CIDR ``` box, and finally click on ``` Apply Configuration ``` button.

![Screenshot 2024-07-01 at 10 42 00](https://github.com/adel-bz/Proxmox-Private-Network/assets/45201934/f901fd47-7fb5-4d7a-b9fa-0d309f4806b5)

3. You must connect to your Proxmox host with SSH or ``` Shell ``` option on Proxmox at the top of the ``` System ``` tap.

4. Put the below code at the end of the ``` /etc/network/interfaces ``` file. (this config is based on my config you maybe should use another network name instead of vmbr1 and enp6s0, also network IP)

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

![Screenshot 2024-07-01 at 10 58 17](https://github.com/adel-bz/Proxmox-Private-Network/assets/45201934/3d251262-94a6-445d-b286-3af7bc738ba9)

5. Restart the network:
```
# sudo systemctl restart networking
```

6. Select your VM on Proxmox panel and in the ``` Hardware ``` section change the network device to vmbr1 (in my case)

7. You should add an IP and gateway to your host if use Cloudinit follow these steps:
- Under the "Hardware" tab go to the cloud-init tab and edit the "IP Config" option like the picture (you can add your specific IP)

![Screenshot 2024-07-01 at 11 13 23](https://github.com/adel-bz/Proxmox-Private-Network/assets/45201934/9dc5f031-bbac-4c74-a4ff-3995b56ebb86)

8. Start Your machine and check the internet connection

```
# Ping 4.2.2.4 
```

example video (it is not the same with those steps).
https://www.youtube.com/watch?v=ITYMeRE455g


