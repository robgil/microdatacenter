# Micro Datacenter Project
This is a project to compile the necessary information to create a personal micro datacenter. This is largely for educational purposes, but can also be used for a personal lab or hosting personal services (website, files, etc)

## What is a micro datacenter?
This is largely opinion, but for the purposes of this project, we'll define it as the following.

_A micro datacenter is a fully functioning, ultra low power, and distributed datacenter. It contains all the functionality of a real datacenter, but small enough that you can run it off a 20A 110V outlet._

Also, it's bare metal...

But why? 

Most folks, myself included, have always run some form of lab at home. This comes with a host of challenges and annoyances.
- Enormous power requirements for anything more than 1/2 a rack of servers
- Expensive, power hungry servers
- Old servers that are out of support, clunky, expensive to fix and upgrade, etc
- Overloading those servers with VMs
- Many folks run VMWare ESX, but that can be pricey
- Upgrades? What's that? 
  - Who actually has the funds to regularly ugprade their lab?

# Draft Design
In this draft design, I'll lay out the rack design and hardware necessary to power dozens of servers. 

## BOM
- [Mobile 1/2 cabinet](https://www.amazon.com/NavePoint-Server-Cabinet-Casters-Shelves/dp/B01I48EJOW/) (or 1/4 cabinet if you want to do a smaller version)
- 2x [6U DIN Mount with cable management](https://www.amazon.com/Rackmount-Din-Rail-Panel-6U/dp/B00SG3PGWK/)
- [Raspberry Pi 4 CM4](https://www.adafruit.com/product/4564)
- [Raspberry Pi 4 Carrier Board](https://www.waveshare.com/cm4-io-base-b.htm)
- POE Switch (Ex. [NETGEAR 24 Port POE Switch](https://www.amazon.com/NETGEAR-24-Port-Gigabit-Ethernet-Unmanaged/dp/B07Z8P4ZPW/))
- [Raspberry Pi DIN Rail Mount](https://www.amazon.com/Winford-Engineering-Raspberry-L-Bracket-Compliant/dp/B083YSWYW1/)
- [NVMe 2242](https://www.amazon.com/Sabrent-DRAM-Less-Internal-Performance-SB-1342-512/dp/B07XVR1KKR/)
- [PoE USB C Splitter](https://www.amazon.com/UCTRONICS-PoE-Splitter-USB-C-Compliant/dp/B087F4QCTR/)

# The Rack
Do you need to do 1/2 a cabinet? No. You could do this smaller. But for this example, I'll do 1/2 a cabinet with room for things like battery backup / UPS

- 1/2 Cabinet: $374.00
- 6U DIN Rack/Cable Mgmt: $199.00

Total: $574.00

# The basic host
The basic host configuration is as follows
- PoE Powered Raspberry Pi
- NVMe Attached Disk
- Vertically mounted on DIN rails
- No case required - better airflow and cooling

This ultra low profile design dispenses with the bulky and failure prone PoE HATs. The PoE hats often have fan failures and take up valuable HAT space for other things. The carrier board in this BOM has the NVMe on the bottom of the board. With this design, the disk and PoE do not take up any HAT space and also don't glog up the rack with USB cables. The NVMe is also attached with PCIe speed rather than USB. 

Cost
- NVMe (512G): 69$
- Raspberry Pi 4 (8G): 75$
- Waveshare Carrier Board: 30$
- DIN Mount: 15$
- Network Port (Switch / Num Ports): $ 11.45 per port (1)
- POE Splitter: 20$

Total: $221.00 / node

Could you get used gear for this cheap? Yes. Would it be more powerful? Yes. But...
- It's bulky as hell (rackmount or old desktop)
- Power hungry
- Is not expandible
- Doesn't grow past the built in number of SATA ports

# Density
With the above BOM (Bill of Materials) we'll be able to get roughly 18 servers in to 6U. This assumes 2" width per server. If you choose to go with SSD instead of NVMe, it can be cheaper, but you'll need additional DIN mounts and it takes up more rack space. Also add on the cost of the USB cables. 

Basically you'd need a 24port switch for every 6U. 

So in 7U, you'd have the switch, and 18 servers, with cable management. 

In that 22U cabinet, you could fit *54 servers*.... now we're talking.

# Server Management and Imaging
[Tinkerbell](https://tinkerbell.org/) is an automated DHCP, NETBOOT, and PXE physical host provisioning service. To keep costs down, you can run this on your desktop in VMs initially. You can also run it on some of your nodes if you have a multi purpose node (something like a shared DHCP, DNS, Tinkerbell, image store, etc)

# Routing
[FRRouting](https://frrouting.org/) is an Open Source routing stack. This is the stack I would recommend for any home lab. This can be run on any linux host and is substantially cheaper than buying or re-using some purpose build switch or router. The goal of this project is to learn the internals, so running FRR is a good way to familiarize yourself with networking protocols. 

As we delve in to more detail, FRR becoming increasingly important to learn cloud networking concepts. I'm not talking about simple concepts like IGWs or VPC Route Tables. I mean the underpinning of VPCs in general. In a later article, I'll describe how you can create your own VPC networking with eVPN and VXLAN using FRRouting. This is the basis for undestanding multi tenant network architecture (hint, it's all encapsulated in the datacenter). 

## Public IP Space 
So you want to learn IPv6? Well, you could get your own ASN and IP space for about $1000.
- ASN: 500$
- /36 IPv6: 500$

_/36 is the smallest ARIN will allocate at the time of writing_

Seems a little steep for most home SREs. But imagine all the money you'll save in not buying rackmount servers or powering them. 

With the routing setup and OpenSwitch, you can advertise your IP space through a provider like Equinix Metal (ask me how I know). You'll be able to have your own publicly routable IPv6 space. You can also use AWS BYOIP and [TransitGateway Connect](https://aws.amazon.com/about-aws/whats-new/2020/12/introducing-aws-transit-gateway-connect-to-simplify-sd-wan-branch-connectivity/)

If you're more adventurous, have a bottomless wallet, or already have IPv4 space, you can do that too. 

## Accessing Your Microdatacenter (no static addressing from your home ISP)
You can use either wireguard or IPSec up to an Equinix Metal host or to AWS (or any other provider that can handle BGP)

# Elastic IPs
Elastic IPs, which you may be familar with from AWS. What this really is is just a public IP that is NAT'd to an ENI/Private IP. How do you achieve this in your microdatacenter? We don't have fancy network cards that offload this, so in order to do this, it's as simple as running BGP on every node. 

For kubernetes workloads, we'll use Cilium to advertise the public IP (Elastic IP) via BGP to the core network and out to the internet (if you're doing public peering)


# Firewall
Firewalls we'll keep simple to understand them. No, we're not going to use PFSense for these exercises. We'll do it the hard way for the purposes of learning. 

nftables, bpfilter, iptables, etc

# k8s
Kubernetes has always been a bit of a struggle in home labs. It's annoying running every service and having enough infrastructure to do so. The balance I've found is utilizing [k3s](https://k3s.io/)

This has support for things like Cilium, which ties in nicely to the routing stack. 

# Distributed Storage
How do you do persistent storage? What can you use for shared storage and object stores? 

[minio](https://min.io/) is the simplest to manage and utilize. It has an S3 like API and is a lot less work than other tools like GlusterFS and Ceph. 

# UPS
Rack Mounted DIY Battery Packs

Yes, we're going _that_ deep. If you want to of course. 

Building batteries from old laptop and car cells is a way to learn how do design power capacity for your datacenter. This is important information if you ever need to design or scope out a cage at a datacenter. Building batteries is also just fun. You can get really creative here with batteries and use all kinds of cells. My personal interest is in batteries that won't catch fire or explode. I'd also prefer batteries that don't off-gas in my office/microdatacenter space. 

If you're not familiar with battery types, it's a great time to learn the difference. My personal favorite is [Lithium-titanate batteries](https://en.wikipedia.org/wiki/Lithium-titanate_battery). These are used in things like weather stations where they are not accessible and have very high recharge cycles. They're basically 20+ year batteries. Their power density isn't as high, but they're safe and won't explode and have a much larger temperature range than typical lithium batteries. LiFePO4 are also safe batteries for this kind of thing. 
