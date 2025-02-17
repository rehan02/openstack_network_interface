# openstack_network_interface
Openstack network interface def in Ubuntu 24.04

In Ubuntu 24.04, `netplan` is the default network configuration tool, and it uses YAML configuration files located in `/etc/netplan/`. Below is how you can configure the network interfaces as described in your original request using `netplan`.

---

### Step 1: Locate the Netplan Configuration File
Netplan configuration files are typically located in `/etc/netplan/`. The file might be named something like `01-netcfg.yaml`, `50-cloud-init.yaml`, or similar. You can list the files in the directory using:

```bash
ls /etc/netplan/
```

Open the file in a text editor with root privileges:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

---

### Step 2: Configure the Management Interface
Assuming the management interface is `eth0`, add the following configuration for a static IP address:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 10.0.0.11/24
      routes:
        - to: 0.0.0.0/0
          via: 10.0.0.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

- `eth0`: The name of the management interface.
- `addresses: 10.0.0.11/24`: Sets the IP address and subnet mask (equivalent to `255.255.255.0`).
- `routes`: Configures the default gateway (`10.0.0.1`).
- `nameservers`: Optional DNS servers (you can customize these).

---

### Step 3: Configure the Provider Interface
For the provider interface (e.g., `eth1`), configure it as a manual interface without an IP address:

```yaml
    eth1:
      dhcp4: no
      dhcp6: no
      optional: true
```

- `eth1`: The name of the provider interface.
- `dhcp4: no` and `dhcp6: no`: Disable DHCP for IPv4 and IPv6.
- `optional: true`: Makes the interface optional (useful for interfaces that may not always be connected).

---

### Step 4: Complete Netplan Configuration File
Here’s how the complete `/etc/netplan/01-netcfg.yaml` file might look:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 10.0.0.11/24
      routes:
        - to: 0.0.0.0/0
          via: 10.0.0.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
    eth1:
      dhcp4: no
      dhcp6: no
      optional: true
```

---

### Step 5: Apply the Configuration
After editing the file, apply the changes using the following command:

```bash
sudo netplan apply
```

---

### Step 6: Verify the Configuration
You can verify the configuration using the following commands:

1. Check the IP addresses:
   ```bash
   ip addr show
   ```

2. Check the routing table:
   ```bash
   ip route show
   ```

3. Check the status of the interfaces:
   ```bash
   networkctl status
   ```

---

### Notes:
- Replace `eth0` and `eth1` with the actual names of your network interfaces. You can find the interface names using:
  ```bash
  ip link show
  ```

- If you are using a different renderer (e.g., `NetworkManager` instead of `networkd`), replace `renderer: networkd` with `renderer: NetworkManager`.

- If you encounter issues, you can debug the configuration with:
  ```bash
  sudo netplan --debug apply
  ```

This configuration will set up the management interface with a static IP and the provider interface without an IP address, as required.

( generated using DeepSeek AI)
