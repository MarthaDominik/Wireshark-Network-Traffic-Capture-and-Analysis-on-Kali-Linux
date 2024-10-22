# **Wireshark: Network Traffic Capture and Analysis on Kali Linux**

## **Introduction**

Wireshark is a powerful network protocol analyzer that I used to capture and analyze network packets in real-time. In this project, I will demonstrate how I installed and configured Wireshark on Kali Linux, captured HTTP, HTTPS, DNS, and ICMP traffic, applied filters to focus on specific traffic types, and saved captured packets for later analysis.

This project will cover the following steps:

- Installing and setting up Wireshark on **Kali Linux**.
- Capturing packets on an Ethernet interface.
- Filtering HTTP, HTTPS, DNS, and ICMP traffic.
- Saving the captured packets for further analysis.

## **Install and Set Up Wireshark on Kali Linux**

Wireshark comes pre-installed with most Kali Linux distributions. However, to enhance security, I needed to configure it to allow packet capturing as a non-root user.

### **Why Configure Wireshark for Non-Root Users?**

#### **Security Risks of Running Wireshark as Root**:

- **Wireshark has a large attack surface**: Since Wireshark processes raw network data, it may encounter malicious or malformed packets. Running Wireshark as root gives it full access to the system, increasing the risk of system compromise through a vulnerability.

- Running **applications as root** generally goes against best practices. If Wireshark or any application is exploited while running as root, the attacker could gain full control over the system.

#### **Principle of Least Privilege**:

- According to the principle of least privilege, users and applications should have only the minimum permissions necessary to perform their tasks. Allowing packet capturing as a non-root user limits the damage that could occur if Wireshark or my user session is compromised.

#### **Best Practices in Kali Linux**:

- **Kali Linux** is widely used in security testing environments. It is essential to minimize the attack surface, and running tools like Wireshark with limited permissions is part of maintaining a secure and hardened system.

### **Steps I took to Set Up Wireshark:**

First, I updated the package list and then made sure Wireshark was installed:

<img width="620" alt="sudo apt update" src="https://github.com/user-attachments/assets/334f251e-6a6b-4540-9752-eb3067c13e86">

<img width="619" alt="sudo apt install wireshark" src="https://github.com/user-attachments/assets/1e20ab39-f9ad-4e06-b41a-a8baa6b2e0a8">

To enable non-root users to capture packets, I ran the following command to reconfigure the package:

<img width="626" alt="sudo dpkg-reconfigure wireshark-common" src="https://github.com/user-attachments/assets/a6c0577e-cdc4-4bf6-9032-58fea59b9a53">

<img width="622" alt="YES" src="https://github.com/user-attachments/assets/b50eac6c-6084-4e29-a4bf-9acb0a5df899">

Next, I added my user to the **wireshark group** to allow packet capturing without root access.

To apply the changes, I rebooted the system.

<img width="623" alt="sudo usermod -aG wireshark $USER" src="https://github.com/user-attachments/assets/ff79a03b-c203-49b2-b9ab-dc7cdbfc8e9e">

After logging back in, I was able to run Wireshark without root privileges:

<img width="727" alt="Run Wireshark" src="https://github.com/user-attachments/assets/5c8fe633-1ecc-47e3-b7d1-898bfcf0d2c2">

Next, I captured packets on the Ethernet interface, generated some network traffic, and saved the captured data for further analysis.

I launched Wireshark and selected the appropriate Ethernet interface (in this case labeled as **eth0**). To start capturing packets, I clicked the **blue shark fin icon**.

I opened **Firefox** and visited some websites to generate network traffic.

<img width="926" alt="Start a Packet Capture on an Ethernet Port" src="https://github.com/user-attachments/assets/fcb03468-ffd6-4adb-922d-c8d165852f71">

Once enough traffic was captured, I returned to Wireshark and clicked the **red square icon** to stop the capture.

<img width="1045" alt="Screenshot 2024-10-15 at 15 19 18" src="https://github.com/user-attachments/assets/a28ba8be-6540-43b1-950b-b1ddc2317e24">

After stopping the capture, I went to **File** → **Save As** and saved the captured packets in .pcap format for later analysis.

Now that I had captured some traffic, I applied a display filter to isolate and analyze only HTTPS traffic, which uses **TCP port 443**.

In the Wireshark filter bar, I typed the following filter to display only HTTPS traffic:

tcp.port == 443

I pressed **Enter** to apply the filter.

After applying the filter, Wireshark displayed only the HTTPS packets. I clicked on individual packets to examine their details.

<img width="1046" alt="HTTPS filter" src="https://github.com/user-attachments/assets/9d60c7f9-862a-4ae1-946a-d6c78f38279f">

Here’s what I examined in the selected packet:

## Ethernet Frame (Layer 2):

- **Source MAC Address**: ce:08:fa:ba:d2:64
- **Destination MAC Address**: b2:88:cf:27:2e:fc

These are the hardware addresses of the devices communicating on the local network. The **source MAC address** is the address of the sending device, and the **destination MAC address** is the receiving device on this specific Ethernet frame.

## Internet Protocol (IP) Layer (Layer 3):

- **Source IP Address**: 142.250.185.68 (which appears to be one of Google’s IP addresses).
- **Destination IP Address**: 192.168.64.3 (the local machine on my network).

This section identifies the sender and recipient of the packet at the network level, showing that my local machine was communicating with a remote server (likely Google, based on the IP address).

## Transmission Control Protocol (TCP) Layer (Layer 4):

- **Source Port**: 443 (which is the default port for HTTPS traffic).
- **Destination Port**: 47478 (an ephemeral port used by the client for this session).
- **Flags**: ACK and Seq=1, Ack=836:
  - **ACK**: The acknowledgement flag indicates that the packet is acknowledging the receipt of a previous packet from the client.
  - **Sequence number** and **Acknowledgement number**: These numbers help track which part of the data is being sent and acknowledged, ensuring reliable transmission over TCP.
- **Length**: 834 bytes: This is the size of the TCP segment.

## TLS Layer (Layer 7):

- The packet contains a **TLSv1.3 “Server Hello”** message. This is part of the initial handshake process in an HTTPS connection, where the server responds to the client’s “Client Hello” message and establishes key exchange parameters to create a secure, encrypted session.
- **Server Hello** details:
  - This packet confirms that the server is responding to the client’s attempt to initiate a secure connection.
  - The server provides its chosen cipher suite, which is used to encrypt the communication.
  - The TLS handshake is crucial in HTTPS as it ensures that data exchanged between the client and server is encrypted and secure.

## Additional Information:

- This packet is part of a larger exchange that sets up the secure connection between the client (my machine) and the server (Google).
- 
<img width="1046" alt="HTTPS filter (1)" src="https://github.com/user-attachments/assets/e5bf9665-0ed5-426a-b8d0-ffc2771c354e">

I decided I will capture and analyze **HTTP traffic** as well, which is unencrypted and transmitted over **port 80**.

I opened **Wireshark**, selected the appropriate network interface, and clicked the **blue shark fin icon** to start capturing packets like in my previous example.

I opened **Firefox** and visited [http://neverssl.com](http://neverssl.com), a website that uses plain HTTP to generate unencrypted traffic.

After the page loaded, I returned to Wireshark and clicked the **red square icon** to stop the capture.

In the filter bar at the top of Wireshark, I applied the following display filter to isolate HTTP traffic:

http && tcp.port == 80


I pressed **Enter** to apply the filter, which displayed only HTTP packets.

### Insight!

#### Capture Filters

- Used before capturing.
- Limit what is captured - allows to save specific types of traffic.
- 
<img width="799" alt="Capture Filter" src="https://github.com/user-attachments/assets/d0df43ca-e116-4752-b9b9-dd67cde9be00">

#### Display Filters

- Used after capturing.
- Let me analyze specific packets from everything I captured, without removing any data.
- 
<img width="1277" alt="Display Filter" src="https://github.com/user-attachments/assets/bcb476eb-7ecf-4eb1-8b59-186413cc9faf">

This filter I applied below is to show packets related to **TLS handshake Client Hello messages**. These messages are crucial for establishing a secure HTTPS connection between the client (machine) and the server (the website I am visiting).

- **TLS (Transport Layer Security)** is used for encrypted communication, such as HTTPS traffic. During the connection process, a TLS handshake takes place, and the **Client Hello** is the first step where the client initiates a secure connection with the server.

- The display filter `tls.handshake.type == 1` isolates and shows packets that contain the **Client Hello** message, which includes information such as:
  - The client’s supported cryptographic algorithms (ciphers).
  - The server the client is trying to reach (indicated by the **SNI** field, which stands for Server Name Indication).
  - The IP address of the server (which helps identify the specific server that the client is connecting to).
  - 
<img width="1045" alt="tls handshake type == 1" src="https://github.com/user-attachments/assets/137cbfe2-c9a1-4596-b5e0-a9f8443a6edf">

- This filter below shows you **all packets** where either the **source IP** or the **destination IP** is `142.250.185.68`.
- This is useful when focusing on all communication between the machine and this specific IP address, which is likely associated with a service or website (in this case, **Google**).
- 
<img width="1047" alt="ip addr == " src="https://github.com/user-attachments/assets/8b2f875d-6e06-4a7f-8ccb-636658b227c6">

This filter shows you **all packets where the source IP address** is `142.250.185.196`.

- In other words, I captured packets that are **sent from** this specific IP address to my machine.
- This filter is useful to analyze all packets being sent from a specific server (in this case, the server at `142.250.185.196`), such as for troubleshooting or monitoring.
- This filter focuses on how a particular server is communicating with your machine.

### Troubleshooting Network Issues

- By focusing only on traffic from this server, I can examine whether there are any retransmissions, packet loss, or delays coming from this specific source. The **duplicate ACKs** might suggest network congestion or issues with data transmission.
- 
<img width="1030" alt="ip src" src="https://github.com/user-attachments/assets/89db4d31-f73c-48d1-b31c-26c8327321c7">

This filter shows **only the packets where the destination IP address** is `142.250.185.196`.

- Only capturing packets that are **being sent to** the IP address `142.250.185.196` (Google-related server).
- It helps isolate the traffic going in one direction (from client to server).
- This is useful for troubleshooting issues with data being sent to the server, such as slow uploads, incomplete transfers, or connection timeouts.
- 
<img width="1037" alt="ip dst" src="https://github.com/user-attachments/assets/d7be9088-3ae8-4f4f-9b31-8ca752a325db">

The logical operator `or` between the two conditions means that Wireshark will display any packet where **either** condition is true. So, it will show:

- Any packet that contains the IP address `142.250.185.163` (regardless of the port used).
- Any packet that is using TCP port `443` (regardless of the IP addresses involved).

This filter is useful for analyzing communication between a client and a server over HTTPS and isolating specific traffic related to a particular IP address that could be interesting for investigation, such as **security analysis** or **network troubleshooting**.

<img width="801" alt="Screenshot 2024-10-16 at 08 28 54" src="https://github.com/user-attachments/assets/4b72a202-13b2-4148-a2a6-221cb3396f2c">

`!(ip.addr == 142.250.185.163)`:

- The `!` (negation) means you **exclude** traffic involving the IP address `142.250.185.163`. Any packets with this IP as the source or destination will not be shown in the results.

`and (tcp.port == 443)`:

- This part ensures you only see traffic using **TCP port 443** (which is the port for HTTPS traffic).

### What This Filter Does:

- **It displays all HTTPS traffic** (i.e., traffic on port 443), **except** for traffic involving the IP address `142.250.185.163`.

This type of filter is useful when you want to focus on HTTPS traffic but want to exclude packets from a specific server (in this case, Google or another web server with the IP `142.250.185.163`).

<img width="874" alt="Screenshot 2024-10-16 at 08 30 13" src="https://github.com/user-attachments/assets/0f0f3fa1-1999-4833-8a57-37cced4b9e77">

`!(ip.addr == 142.250.185.163)`:

- This part of the filter **excludes any packets** where the IP address is `142.250.185.163` as either the source or the destination. The `!` symbol is used to negate or exclude results. So, any communication involving the IP `142.250.185.163` will not be displayed.

`(tcp.port == 443 or tcp.port == 80)`:

- This part of the filter **includes packets** that are using **TCP port 443** (which is for HTTPS traffic) **or TCP port 80** (which is for HTTP traffic). This ensures that only web traffic (either encrypted via HTTPS or unencrypted via HTTP) is shown.

I focused on the data being exchanged over HTTP/HTTPS without being distracted by a known or irrelevant server. Analyzed traffic but excluded noise from a specific IP that I am not interested in.

This is useful for fine-tuning network traffic captures, especially when dealing with lots of web traffic and needing to exclude irrelevant servers.

<img width="875" alt="HEY" src="https://github.com/user-attachments/assets/45128c7b-2ec4-437f-9411-96912429f6e2">

This filter captures all DNS (Domain Name System) traffic. DNS is responsible for translating human-readable domain names (like `www.google.com`) into IP addresses that computers use to identify each other on the network.

- **Purpose**: Capturing DNS traffic is useful for identifying which domain names are being queried by a machine. This can be useful for understanding what sites or services a device is trying to reach, which is often relevant in network forensics or troubleshooting domain resolution issues.
- 
<img width="879" alt="Screenshot 2024-10-16 at 08 37 28" src="https://github.com/user-attachments/assets/63c0fdc6-080c-4a15-9f34-dc3f54174cab">


Next, the example shows I have captured **ICMP (Internet Control Message Protocol)** packets, commonly used for operations like network diagnostics (e.g., ping commands). Here’s an explanation of what you captured:

### Source and Destination:

- The source IP address is **192.168.64.3**, which seems to be the local machine.
- The destination IP is **8.8.8.8**, which is a well-known public DNS server provided by Google.

### Protocol:

- The packets are classified as **ICMP** with the message type **Echo (ping)**, indicating that the system is sending ICMP Echo requests (ping) and receiving Echo replies back from the destination (Google’s DNS server).

### Packet Size:

- The length of each packet is **98 bytes**, typical for ICMP ping requests/replies, including headers and payload.

### Purpose:

- This captures the exchange between your machine and Google’s DNS server, confirming network connectivity by sending a series of pings and receiving responses. I generated traffic by pinging.
- 
<img width="876" alt="Screenshot 2024-10-16 at 10 10 25" src="https://github.com/user-attachments/assets/2947af03-0d50-4874-ba11-c9eb10ad03a2">

### Summary of Project:

In this project, I explored how to use **Wireshark** for network traffic capture and analysis on **Kali Linux**, showcasing key network protocols such as HTTP, HTTPS, DNS, and ICMP.

Starting with the installation and configuration of Wireshark, I ensured that it could run securely without root privileges by adding my user to the **Wireshark group** and configuring it to capture packets safely. I then proceeded to capture traffic on an Ethernet interface by generating network activity through browsing websites and using tools like **ping**.

I applied various **display filters** to narrow down the traffic I wanted to analyze. For instance, I filtered HTTPS traffic to examine the **TLS handshake** process, capturing critical details like **Client Hello** and **Server Hello** messages. I also captured DNS traffic to see how domain names were resolved and ICMP traffic to observe ping requests and replies.

Throughout the project, I emphasized the importance of **capture filters** to focus on specific traffic before capturing and **display filters** to analyze specific packets after capturing. By capturing and analyzing different types of network traffic, I demonstrated practical skills in **network traffic analysis**, essential for identifying and diagnosing network-related issues, and ensuring secure communication between clients and servers.

This project highlights my knowledge in network analysis and my ability to utilize Wireshark effectively, which is crucial for roles in **network security**, **threat detection**, and **troubleshooting network issues**.


## Contributing

We welcome contributions from the community. If you have any suggestions, bug reports, or feature requests, please open an issue or submit a pull request. 

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contact

For any inquiries or further information, please contact me at [martaa.dominik@gmail.com](mailto:martaa.dominik@gmail.com).

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/marta-dominik-a67803233/)
