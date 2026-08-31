# CHAPTER 8 Networking

Task 1. Configure the network interface with the following settings:
IPv4 Address: 192.168.122.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.122.1 
DNS Server: 192.168.100.1
Interface Name: eth0
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

Solution 1. with nmcli

a. Configure with nmcli
    nmcli connection modify "ethe" \
    ipv4.method manual \
    ipv4.addresses 192.168.122.10/24 \
    ipv4.gateway 192.168.122.1 \
    ipv4.dns 192.168.100.1

a.1 Check and change the hostname
    nmcli general hostname
    nmcli general hostname <new_name>

b. Connection up
    nmcli connection up "ethe"

c. Verify
    ip addr show

d. Router verify
    ip route

Solution 2. With nmtui

1. Open with sudo a nmtui UI tool for network connection
    sudo nmtui
2. Press on the Edit conenction (normally first btn)
3. Find the required Interface name to be edited:
    NOte: the profile name may include usually the interface name, thus you will see:
        Profile name: System eth0
        DEvice: eth0 (MAC here)
4. Find field IPv4 CONFIGURATION and set it as Manual currently it is auto now.
5. Then new fields are poped up: 
    Addresses: add here the Ipv4 Address + IMPORTANT ! the mask, hence , based on the data given:
        
        IPv4 Address: 192.168.122.10
        Subnet Mask: 255.255.255.0

    You need to put in the Addresses as: 192.168.122.10/24 and press enter

6. Add Gateway to the Gateway
7. Add DNS servers to the DNS servers
8. Important be sure htat the device name is the one which is really exist, otherwise you won't be able to activate it.
9. Go down for OK btn and press it
10. Come to the root menu of the TUI
11. Select Activate connection
12. Deactivate the old connection or directly Activate that one which is requested. Means deactive at first and then activate it again.
13. Select the Set system hostname and enter the requested hostname

Note: on the exam it can be a misconfusion that you won't be told which exact interface need to edit. --> thus you need to edit the one and only one which is exist now. Before to go with the nmtui check the interfaces with:
ip a and ip r

Note:
NETWORK NOT WORKING?

1. Interface?
   ip link

2. IP address?
   ip addr

3. Route?
   ip route

4. Can I reach gateway?
   ping <gateway>

5. Can I reach external IP?
   ping 8.8.8.8

6. Can I resolve names?
   DNS / resolvectl

# Chapter 9 Managing Softwares

