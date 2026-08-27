
# CHAPTER <some-number> Networking

Task 1. Configure the network interface with the following settings:
IPv4 Address: 192.168.122.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.122.1 
DNS Server: 192.168.100.1
Interface Name: ethe
Hostname: rhcsa-node

Note: it is prefferably to do with the nmtui, and to not spend much time on this and just move on, but it is good to know of what you are doing, thus the nmcli is good to understand as well. 

Useful commands:

a. Check the actual profile configs:
    nmcli conenction show
b. Actual addresses present on the interface
    ip addr
c. Actual routing table
    ip route
d. CHeck the configs of the current profile config by name
    nmcli connection show "<name_of_profile_config>"
e. Update connection to proper device:
    nmcli connection modify static connection.interface-name ens160
f. Alway try to up after modification:
    nmcli connection up "<name_connection>"

Solution 1. 

a. Configure with nmcli
    nmcli connection modify "ethe" \
    ipv4.method manual \
    ipv4.addresses 192.168.122.10/24 \
    ipv4.gateway 192.168.122.1 \
    ipv4.dns 192.168.100.1

b. Connection up
    nmcli connection up "ethe"

c. Verify
    ip addr show

d. Router verify
    ip route