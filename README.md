 Just finished coding this DHCP flooder and thought I'd share how it works!

This is obviously for educational purposes only, but it's crazy how most routers (even enterprise-grade ones) aren't properly configured to handle DHCP packets and remain vulnerable to fake DHCP flooding.

The code is pretty straightforward but efficient. I'm using C++ with multithreading to maximize packet throughput. Here's what's happening under the hood: First, I create a packet pool of 1024 pre-initialized DHCP discovery packets to avoid constant reallocation. Each packet gets a randomized MAC address (starting with 52:54:00 prefix) and transaction ID. The real thing happens in the multithreaded approach, I spawn twice as many threads as CPU cores, with each thread sending a continuous stream of DHCP discover packets via UDP broadcast.

Every 1000 packets, the code refreshes the MAC address and transaction ID to ensure variety. To minimize contention, each thread maintains its own packet counter and only periodically updates the global counter. I'm using atomic variables and memory ordering to ensure proper synchronization without excessive overhead. The display thread shows real-time statistics every second, total packets sent, current rate, and average rate since start. My tests show it can easily push tens of thousands of packets per second on modest hardware with LAN.

The socket setup is pretty basic, creating a UDP socket with broadcast permission and sending to port 67 (standard DHCP server port). What surprised me was how easily this can overwhelm improperly configured networks. Without proper DHCP snooping or rate limiting, this kind of traffic can eat up all available DHCP leases and cause the clients to fail connecting and ofc no access to internet. The router will be too busy dealing with the fake packets that it ignores the actual clients lol. When you stop the code, the servers will go back to normal after a couple of minutes though.

Edit: I'm using raspberry pi to automatically run the code when it detects a LAN HAHAHA.
r/hacking - Coded a DHCP starvation code in c++ and brought down my home router lol


Not sure if I should share the exact code, well for obvious reasons lmao. 


