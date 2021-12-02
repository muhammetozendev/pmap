# Description

Pmap is a tool that replicates nmap to find open ports of computers on a given network. It's similar to the connect scan of nmap since it connects to the target and interprets the result. But the difference lies in speed. There are cases where nmap is much faster that pmap (which is the case for most scans when there's no proxy in place), but this program runs much faster with proxies. It's not a replacement for nmap, and when you're not passing a scan through a proxy, nmap is definitely a better choice. But if the scan goes through some type of proxy, nmap waits for one port to respond before it moves on to another. That's where pmap comes in handy.

# How it works

Pmap relies on creating concurrent multiple threads and sending the connection request to the target computer simultaneously. An important thing to note here about how nmap works with proxies is that nmap scans don't work with a proxy when -sS flag is set becuase SYN scan creates a crafted TCP packet and the local TCP stack doesn't even know about it. Since it's not a legitimate TCP packet, it cannot be passed through a proxy. Only TCP/Connect scan can be used in combination with proxies. But the downside of using connect scan is that it has to wait for one port to respond before it moves on to another when using a proxy. However, in this program, we use socket.connect_ex() function which creates a legitimate connection to the target port. But what makes it really fast is the fact that it runs on multiple threads (50 by default), meaning it opens concurrent connections to multiple ports of the target at the same time to improve the performance of the scan.
Threading is especially useful when passing the scan through the tor network or some other proxy server since we don't have to wait for one port to respond before going to another. The tool allows us to create as many threads as we want but it's not recommended to keep it more than 100.

# How to use it

We need to specify a target to scan. That can be a domain, an IP address, or a subnet in CIDR notation. After specifying the target, we should give pmap the ports to be scanned. The port specification is the same as nmap. A dash "-" indicates a port range and a comma "," can be used to specify additional ports by separating them from the previous port range or individual port. An example of that is as follows:

> python3 pmap example.com -p 10

Instead of -p option, we can also use -t followed by a number that specifies the number of top most commonly used ports in TCP. This top port order is taken from nmap. The command below scans the top 50 ports: 

> python3 pmap example.com -t 50

We can also specify IP blocks or an individual IP address instead of a domain name as follows:

> python3 pmap example.com/24 
> python3 pmap 127.0.0.1/24

Lastly, pmap also allows for the specification of the number of concurrent threads. This will tell pmap how many ports to scan at a time. If you leave this option out, it'll default to 50. It should also be noted that, unlike nmap, pmap doesn't support parallel host scanning yet. But that's something I could be adding in the future.
