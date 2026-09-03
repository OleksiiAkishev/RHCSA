# CHAPTER 8 — Networking

## Task 1 — Configure a network interface
Configure the interface with these settings:
- IPv4 address: `192.168.122.10/24`
- Subnet mask: `255.255.255.0` (same as `/24`)
- Default gateway: `192.168.122.1`
- DNS server: `192.168.100.1`
- Interface name: `eth0` (profile may vary)
- Hostname: `rhcsa-node`

Note: `nmtui` is quickest for the exam; `nmcli` is useful to understand and script.

### Useful commands
- Show connection profiles:

```bash
nmcli connection show
```

- Show addresses on interfaces:

```bash
ip addr
```

- Show routing table:

```bash
ip route
```

- Show details for a profile (replace name):

```bash
nmcli connection show "<profile_name>"
```

- Update connection's device name:

```bash
nmcli connection modify <profile_name> connection.interface-name ens160
```

- Reactivate a connection after changes:

```bash
nmcli connection up "<profile_name>"
```

## Solution A — Using `nmcli`

1. Modify the connection (replace `<profile>` with the actual profile name):

```bash
nmcli connection modify "<profile>" \
  ipv4.method manual \
  ipv4.addresses 192.168.122.10/24 \
  ipv4.gateway 192.168.122.1 \
  ipv4.dns 192.168.100.1
```

2. Set the hostname:

```bash
nmcli general hostname rhcsa-node
```

3. Bring the connection up:

```bash
nmcli connection up "<profile>"
```

4. Verify:

```bash
ip addr show
ip route
```

## Solution B — Using `nmtui` (interactive)

1. Start the TUI:

```bash
sudo nmtui
```

2. Choose *Edit a connection*, select the profile for the interface you want to change (profile names commonly include the interface name).
3. Change *IPv4 Configuration* to *Manual* and set the `Addresses` field to `192.168.122.10/24`.
4. Fill in *Gateway* (`192.168.122.1`) and *DNS servers* (`192.168.100.1`).
5. Save, then choose *Activate a connection* and activate the updated profile.
6. Set the system hostname from the TUI (`Set system hostname`) or with `nmcli` as above.

Tip: run `ip a` and `ip r` before starting `nmtui` to identify the active interface.

## Troubleshooting checklist
- Is the interface present?

```bash
ip link
```

- Does the interface have the expected IP?

```bash
ip addr
```

- Is there a route to the gateway?

```bash
ip route
```

- Can you reach the gateway?

```bash
ping -c 3 192.168.122.1
```

- Can you reach an external IP?

```bash
ping -c 3 8.8.8.8
```

- Can you resolve DNS names?

```bash
resolvectl query google.com
```


# CHAPTER 9 — Managing Software

