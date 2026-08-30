# Active_Reconnaissance

<details open>
<summary>📋 Note Metadata</summary>

<dl>
<dt><b>Main Topic</b></dt><dd>Offensive_Security</dd>
<dt><b>Date</b></dt><dd>2026-03-22</dd>
<dt><b>Resources</b></dt><dd>https://tryhackme.com/room/activerecon</dd>
<dt><b>Related Notes</b></dt><dd></dd>
<dt><b>Status</b></dt><dd>status/wip</dd>
<dt><b>Tags</b></dt><dd></dd>
</dl>

</details>

---

Technically speaking, ping falls under the protocol ICMP (Internet Control Message Protocol). ICMP supports many types of queries, but, in particular, we are interested in ping (ICMP echo/type 8) and ping reply (ICMP echo reply/type 0). Getting into ICMP details is not required to use ping.

Generally speaking, when we don’t get a ping reply back, there are a few explanations that would explain why we didn’t get a ping reply, for example:

- The destination computer is not responsive; possibly still booting up or turned off, or the OS has crashed.
- It is unplugged from the network, or there is a faulty network device across the path.
- A firewall is configured to block such packets. The firewall might be a piece of software running on the system itself or a separate network appliance. Note that MS Windows- blocks ping by default.
- Your system is unplugged from the network.
  
---

In this room, we have covered many various tools. It is easy to put a few of them together via a shell script to build a primitive network and system scanner. You can use `traceroute` to map the path to the target, `ping` to check if the target system responds to ICMP Echo, and `telnet` to check which ports are open and reachable by attempting to connect to them. Available scanners do this at much more advanced and sophisticated levels, as we will see in the next four rooms with `nmap`.

|Command|Example|
|---|---|
|ping|`ping -c 10 10.114.164.143` on Linux or macOS|
|ping|`ping -n 10 10.114.164.143` on MS Windows|
|traceroute|`traceroute 10.114.164.143` on Linux or macOS|
|tracert|`tracert 10.114.164.143` on MS Windows|
|telnet|`telnet 10.114.164.143 PORT_NUMBER`|
|netcat as client|`nc 10.114.164.143 PORT_NUMBER`|
|netcat as server|`nc -lvnp PORT_NUMBER`|

