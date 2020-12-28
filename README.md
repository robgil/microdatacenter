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
- Old servers that are out of support, clunky, old, etc
- Overloading those servers with VMs
- Many folks run VMWare ESX, but that can be pricey
- Upgrades? What's that? 
  - Who actually has the funds to regularly ugprade their lab?

# Draft Design
In this draft design, I'll lay out the rack design and hardware necessary to power dozens of servers. 

## BOM
- [Mobile 1/2 cabinet](https://www.amazon.com/NavePoint-Server-Cabinet-Casters-Shelves/dp/B01I48EJOW/) (or 1/4 cabinet if you want to do a smaller version)
- 2x [6U DIN Mount with cable management](https://www.amazon.com/Rackmount-Din-Rail-Panel-6U/dp/B00SG3PGWK/)
- [Raspberry Pi 4](https://www.adafruit.com/product/4564)
- [Raspberry Pi PoE HAT](https://www.raspberrypi.org/products/poe-hat/)
- POE Switch (Ex. [NETGEAR 24 Port POE Switch](https://www.amazon.com/NETGEAR-24-Port-Gigabit-Ethernet-Unmanaged/dp/B07Z8P4ZPW/))
- [Raspberry Pi DIN Rail Mount](https://shop.inux3d.com/en/home/50-terrapi-d-din-rail-terrapi-modular-system-for-raspberry-pi.html)
- [USB to SATA Cable](https://shop.inux3d.com/en/home/28-55-25-sata-to-usb-adapter-with-cable-for-ssd-and-hard-drive-usb-30-sata-iii-black.html#/11-color-black)
- [2.5inch SSD](https://www.amazon.com/Samsung-500GB-Internal-MZ-76E500B-AM/dp/B0781Z7Y3S/)

# The Rack
Do you need to do 1/2 a cabinet? No. You could do this smaller. But for this example, I'll do 1/2 a cabinet with room for things like battery backup / UPS

- 1/2 Cabinet: $374.00
- 6U DIN Rack/Cable Mgmt: $199.00

Total: $574.00

# The basic host
The basic host configuration is as follows
- PoE Powered Raspberry Pi
- Additional USB SSD disk for your workloads
- Vertically mounted on DIN rails
- No case required - better airflow and cooling

Cost
- SSD: 54$
- Raspberry Pi 4 (8G): 75$
- DIN Mounts for both: 15$
- Network Port (Switch / Num Ports): $ 11.45 per port (1)

Total: $155.45

Could you get used gear for this cheap? Yes. Would it be more powerful? Yes. But...
- It's bulky as hell (rackmount or old desktop)
- Power hungry
- Is not expandible
- Doesn't grow past the built in number of SATA ports

# Density
With the above BOM (Bill of Materials) we'll be able to get roughly 18 servers in to 6U. This assumes 2" width per server. If you don't do SSDs, you can go denser. 

Basically you'd need a 24port switch for every 6U. 

So in 7U, you'd have the switch, and 18 servers, with cable management. 

In that 22U cabinet, you could fit *54 servers*.... now we're talking.

# Routing
FRRouting

# Elastic IPs
FRRouting with /32s

# Firewall
nftables, bpfilter, iptables, etc

# k8s

# Distributed Storage
MinIO

# UPS
Rack Mounted DIY Battery Packs
