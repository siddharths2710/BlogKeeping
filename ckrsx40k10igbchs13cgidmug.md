## Powerful Reverse Proxies, VLANs and Tunneling Solutions for Developers

# Introduction


**CAUTION**: Hyperlink overload!

I've been exploring tons of ways to reuse my old personal laptop, and from a developer standpoint, there are tons of services that we can deploy for productively utilizing the available compute and memory:

![kodi.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1627802425563/LMY5rITxu.jpeg)

-  [Media Center](https://techhut.tv/turn-laptop-media-server-ubuntu-plex/), easy setup, minimal fuss ( with the exclusion of   [Kodi](https://www.makeuseof.com/tag/set-up-use-kodi-beginners/) ), and serves my collection remotely (using port forwarding/UPnP/NAT-PMP).
- 24/7 Web Server Hosting, with port forwarding and a  [free domain](https://www.freenom.com) setup
- Static file and webpage hosting ( a Pythonian can simply issue a `python3 -m http.server` )
- Publicly exposed FTP/NAS server to facilitate dedicated file transfers
- Personal VPN, setup preferably as an [IPSec/OpenVPN/Wireguard](https://www.ivpn.net/pptp-vs-ipsec-ikev2-vs-openvpn-vs-wireguard/) responder, and exposing the dev endpoint publicly using [Dynamic DNS](https://www.noip.com/) solutions.
- My most favorite use-case, being the ability to remotely program on this dev machine, by setting up an open-source  [bowser-frontend for VSCode](https://github.com/cdr/code-server) (We could possibly be programming on the cloud in the near future, provided  [GitHub Codespaces](https://github.com/features/codespaces) evolves )


![gh_codespaces.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627802574584/_iCxiG1yS.png)

Regardless of the endless possibilities(as mentioned above), my major requirement is to expose any service as a public endpoint, so that I can remotely access it from any geographic location (think Starbucks). 

# Background

The most straightforward solution at hand is to simply expose the service to the public through standard  [port forwarding](https://www.noip.com/support/knowledgebase/general-port-forwarding-guide/), and preferably mapping your public-facing IPv4 address to a more readable URL using [DNS Services](https://freedns.afraid.org/) (Modern home routers also support [Dynamic DNS](https://www.tp-link.com/us/support/faq/1367/) as well, removing the hassle of frequently updating the DNS record to the current DHCP assigned IPv4 address.

While following the above approach, I have encountered the unintended consequence of the  [Double NAT](https://helpdeskgeek.com/networking/what-is-double-nat-and-how-to-fix-it-on-a-network/) problem:

![double-nat.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627802709830/tLGI6PY8O.png)

- The end router assigns addresses within acceptable [private IP address classes](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) to the home devices
- The gateway of the end router is an address that also belongs to the private IP address space. This follows that the intermediate ISP network device (typically classified as a  [DMZ](https://searchsecurity.techtarget.com/definition/DMZ) ) is also present within a private network
-   This makes port forwarding impossible, and unless my ISP can set up a static IP at either of the routers with less overhead and expense, I've had to research on other alternatives which can conveniently make any service hosted by my machine as public-facing.

This similar problem has a bigger impact on the gaming community, and unless you're within reach of your DMZ, there's nothing much you can do about this scenario since you're at the mercy of your ISP's medieval networking infrastructure. 

# Solutions

Keeping this above bottleneck in mind, I've explored several solutions which can pass off for bypassing the Double-NAT problem, depending on the exact use-case and requirement.

## VLAN-based Private Hosted Networks

These are ideal if you wish to interact with only a fixed number of remote network devices. Consider my current setup as depicted below:



![Screenshot from 2021-08-01 00-09-39.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627756812515/gLBvjHoDR.png)

In the above diagram, the two host nodes are present in remote locations, and belong to a private network hosted on the web, and connectivity between the nodes is achievable through a tunneling mechanism. Solutions in this category usually adopt a network topology similar to that used by enterprises and large firms to link their branch offices spread across different geographic locations (predominantly a **Site-to-Site** connectivity model)

- [Zerotier](https://www.zerotier.com) is the most popular, open-source **SD-WAN** based network management and hosting solution using P2P tunneling.
- This offers a private network for direct connectivity with trusted network devices situated at different geographic locations, which means that any service you host on one of your devices can be accessed remotely by the other devices within the network.
- Every participating node is assigned a private IPv4 address corresponding to a newly created tunnel interface bundled up with the Zerotier CLI.
- An alternative to Zerotier is [Tunngle](https://twitter.com/tunngle), which has been widely used for simulating LAN parties across boundaries for multiplayer gaming. Tunngle however supports limited platforms, and unfortunately stopped receiving further support from the developers. 
- Another competitor is [Hamachi](https://www.vpn.net/), which allows you to extend LAN-like networks to geographic locations, but its implementation has been heavily scrutinized, since its duplex VPN-like nature could make an outsider gain untrusted connection to the system as well.

## Self-hosting proxies

These are reverse proxy-based solutions that allow an end-user to expose local services behind a firewall and NAT(or double-NAT in this case)  over the Internet, without port forwarding or any other additional configuration required in the private/next-hop routers. These solutions typically accommodate Web-based services using TCP/HTTP protocols, and most of them simply require you to execute a binary with permissions as necessary, and even map your public-facing IPv4/6 address with a domain. Popular software in this space are [ngrok](https://ngrok.com/), [localtunnel](https://localtunnel.me), [frp](https://github.com/fatedier/frp) and [expose](https://expose.dev).

## SSH-Tunneling based Solutions

![SSH-tunneling](https://cdn.hashnode.com/res/hashnode/image/upload/v1627802236333/O5MeDmEqd.png)
SSH is a powerful backbone that is leveraged for the use-cases already discussed, such as exposing ad-hoc services without dealing with firewalls/VPNs/port forwards, simulating private P2P networks, and other problems which are solved using reverse-proxies.

[Serveo](https://serveo.net) seems to be the most popular solution for a long time, although it hasn't received any support over the years. Other popular projects are [sshuttle](https://github.com/sshuttle/sshuttle), [chisel](https://github.com/jpillora/chisel), [sish](https://ssi.sh/) and [teleconsole](https://www.teleconsole.com/).

## Conclusion

We've predominantly investigated ways to: 
- publicly expose services running locally on any dev machine
- Privately connect with other machines/nodes using Tunneling 

Do check out the awesome one-stop Bible of all solutions on  [Awesome Tunneling](https://github.com/anderspitman/awesome-tunneling) maintained by [Anders Pitman](https://apitman.com/). 