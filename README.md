# Troubleshooting Command 

## 1. Ifconfig
`Ifconfig` displays the network configuration and DHCP, DNS settings. It also diagnoses networking problems and provides information about the interfaces.

### Media State
- **Ethernet Adapter Ethernet**: Shows "Media disconnected" indicating the network adapter is disabled. No DNS Suffix is present.
- **Ethernet Adapter VirtualBox Host-only Network**: The link-local IPv6 is assigned for communication within the network segment. The Default Gateway is not assigned, indicating the adapter is used for communication within the subnet only.
- **Wireless LAN Adapter Wi-Fi**: Connection-specific DNS suffix is established for DNS name resolution. The Default Gateway is assigned for routing outside the local network.
![image](https://github.com/user-attachments/assets/e2e6203d-5db6-456c-bc8d-c4943fd4f443)
![image](https://github.com/user-attachments/assets/828658fb-923e-4587-905a-a6ed1d38bc43)

## Options
### `/all`
- Provides detailed parameters such as the node type, WINS proxy, and IP routing enabled status, DHCPv6 data (IAID, Client DUID, NetBIOS).
- In Windows IP config, the node type is often undetermined if no configured node type for resolving NetBIOS names exists.
- WINS resolves NetBIOS names to IP addresses, acting as an intermediary between WINS client and server.
- `NetBIOS over TCP/IP` is a protocol that allows legacy NetBIOS services to be used over modern TCP/IP networks.
![image](https://github.com/user-attachments/assets/8ce49096-37a8-4724-950b-4ff21b4d8e83)
![image](https://github.com/user-attachments/assets/129549e8-8348-49b3-8f0c-7eb76e121f02)


### `/renew`
- Reassigns the IP address by the DHCP server. If network interfaces are physically disconnected, the operation is prevented.
![image](https://github.com/user-attachments/assets/12d4c148-774c-49fc-a91d-b812f55d870e)

### `/release`
- Releases the IP address assigned to the networks, giving it back to the DHCP server. The link-local address is automatically configured and does not come from the DHCP server.
![image](https://github.com/user-attachments/assets/38759237-7f9f-4581-8711-662b700b71c7)
![image](https://github.com/user-attachments/assets/13c51788-1b48-481f-b452-0a8c16205234)

### `/flushdns`
- Resets the contents of the client’s resolver cache.
![image](https://github.com/user-attachments/assets/4acaa312-df2b-4fc7-8859-2ae06a71959f)

### `/displaydns`
- Shows recorded DNS data such as the name, type (1 indicates A record), TTL, Data length (number of bytes, default 4), and section which contains the query data.
![image](https://github.com/user-attachments/assets/ce85d4aa-4b21-46c6-aa39-762ae24ef91f)

### `/registerdns`
- Manually updates the DNS records for the host, ensuring the resolution of IP and hostname is registered with the DNS server.
![image](https://github.com/user-attachments/assets/d7b9453a-46b6-4121-ab3e-64dd91c40e0f)

## 2. Ping

Ping (Packet Internet Groper) is used to ensure the destination IP address exists and is operational. It uses ICMP echo requests to the network interface.
![image](https://github.com/user-attachments/assets/1cfea99c-f539-418a-99e7-616af357c28e)
![image](https://github.com/user-attachments/assets/146195cc-5d31-4795-aee4-3a8e8b2216b5)

By default, four ICMP requests are sent to verify the reachability of a destination. When attempting to connect to `10.10.10.10`, the response was a request timeout. However, when pinging `8.8.8.8` (Google Public DNS), the attempt was successful, and the response included the time taken for each packet and its Time to Live (TTL). The round-trip time was also calculated.

- Using the `-n` option allows us to send a specific number of ICMP requests to the destination. In this case, 5 requests were sent.
![image](https://github.com/user-attachments/assets/987ec583-4f8d-4eef-93c2-0b36ca1fcc40)

- The `-f` option sets the Do Not Fragment bit.
![image](https://github.com/user-attachments/assets/f825afb9-6b67-458f-99b5-b76b06470052)

- The `-w` option controls the timeout. When the default 1 (1000ms) is set, the response is received. But when it is adjusted to 0.01 (10ms), the response states "Transmit failed - General Failure" due to the very short timeout causing the ping requests to fail.
![image](https://github.com/user-attachments/assets/e426aed3-bb1c-449c-915e-5fda0ca24985)
![image](https://github.com/user-attachments/assets/060c1264-47ba-42ef-a7be-665ae635bf16)

- The `-l` adjusts the size of the packet sent during the request. When the size is set at 64 bits, the time taken to reach the destination is longer than for a 32-bit packet.
![image](https://github.com/user-attachments/assets/c4a55954-f2c2-41c6-b5c1-8373c51e55c3)
![image](https://github.com/user-attachments/assets/db989503-8272-4bbc-87e4-bfc35398fb5d)

## 3. Tracert

Tracert (Traceroute) is a command used for troubleshooting larger networks involving multiple intermediate network components. It uses ICMP packets to send echo requests to the destination and calculates the number of hops involved.
![image](https://github.com/user-attachments/assets/8560e600-a539-45c0-995b-02fdd71d6643)

The three columns of time represent the round trip of ICMP packets to each hop. If the time is higher, it might be due to network congestion. `*` represents that the response is not received and it’s a request timed out.

- The usage of `-d` option speeds up the process by preventing the resolution of IP address to hostname.
![image](https://github.com/user-attachments/assets/1c57246a-08c3-40da-83f4-b5256c1f1770)

## 4. Nslookup

Nslookup (Name Server Lookup) queries the DNS for mapping information of a domain name and IP address or any other DNS records.
![image](https://github.com/user-attachments/assets/bc9e723e-7787-4c3b-84e2-9bacc804a325)

Here the DNS server used to resolve the domain name is given as Server and the corresponding address. The non-authoritative answer means it has not been received from the authoritative server but from the cache.

To obtain the record from the authoritative server, the `-type=soa` is used, which provides information such as the primary DNS server, the timing parameters, DNS zone file version number (2342648264). The retry time is before the secondary server can check for updates if the attempt failed.
![image](https://github.com/user-attachments/assets/0724afa3-4450-4bc5-a72c-0011a34f6d34)

## 5. Netstat

Netstat is a utility used to display routing tables, interface statistics, masquerade connections, etc. It allows monitoring of network performance.
![image](https://github.com/user-attachments/assets/20e3d5b7-46ef-4b8f-b88d-37ee385f5deb)

The Local Address is the local machine’s IP address and its corresponding port number. The Foreign Address is the endpoint of a network connection. The State parameter explains:
- `syn_received`: TCP connection request has been received from the remote but hasn’t completed the connection.
- `established`: Connection is active and open.
- `close_wait`: The connection is closed by the remote side.
- `time_wait`: Though the connection has been closed, the local machine is waiting to ensure the remote machine has received the ack packet.
- `syn_sent`: Connection request has been sent by the local machine.

Options:
- The `-e` option displays Ethernet statistics, showing the number of packets and bytes sent and received, as well as errors and discarded packet information.
![image](https://github.com/user-attachments/assets/675fa828-a9d1-4837-8ccf-37bfe1024876)

- The `-n` option displays network connections, routing tables, interface statistics, masquerade connection, and multicast memberships, showing numerical addresses and port numbers instead of resolving to host and service names.
![image](https://github.com/user-attachments/assets/2e5c6762-b02c-4070-b630-c010be0eaab2)

- The `-o` option displays network connections along with the Process ID for each connection.
![image](https://github.com/user-attachments/assets/4fd8ec64-6c34-45b3-a6cb-b75f9e7b5787)

- The `-p` option lists the connections established by a particular protocol. When `-p udp` is executed, no data was recorded as the local host didn’t handle any UDP traffic.
![image](https://github.com/user-attachments/assets/793e7f6a-11ff-4be3-b49a-6d513b97a0f8)

- The `netstat -s` command offers detailed statistics for multiple network protocols on a system, including IPv4 and IPv6 metrics, ICMP, TCP, and UDP activity.
![image](https://github.com/user-attachments/assets/5754a923-1018-4a9b-ba2c-45da1b88fcfe)
![image](https://github.com/user-attachments/assets/65120c78-40d5-48c2-bfa7-92146a7085af)
![image](https://github.com/user-attachments/assets/4e9a67db-f222-4c1c-97e2-21826c4caf38)

- The `-r` command displays the routing table, showing how network packets are directed to their destinations.
![image](https://github.com/user-attachments/assets/3b9c9ad7-c4cd-4bbb-be7e-b7bfc2671ebd)
![image](https://github.com/user-attachments/assets/ffc0ff08-4532-45eb-80dc-bde36c09c82a)

## 6. ARP

ARP is used to map IP addresses to MAC addresses, which is used by network hardware to locate devices on the same local network.
![image](https://github.com/user-attachments/assets/51cd3f9a-0ee0-4450-abf9-a83ba429fe99)
![image](https://github.com/user-attachments/assets/700b245d-f125-4986-a89e-fcd3d6001bf5)


- The `-a` option provides information for each network interface, including the internet address, physical address, and type. Entries marked as "static" are manually configured, while those marked as "dynamic" are automatically learned through ARP requests.
