# Multi-container-host-networking-using-VxLan-overlay-network
Environment setup: At first you have to take two virtual machine and virtual machine should ping one another. I am using centos9 for this demo and I have taken two Centos9 virtual machines from ec2 instances. For Ec2 security group I am allowing all traffic to cope up with connectivity problem. Two virtual machine should be in same vpc so that you can ping one from another.Here is the security group picture


<img width="713" alt="security group" src="https://github.com/nobelrakib/Multi-container-host-networking-using-VxLan-overlay-network/assets/53372696/3c7c55d0-5299-4a26-842d-995a45daf819">

What we are going to make

<img width="469" alt="overlay" src="https://github.com/nobelrakib/Multi-container-host-networking-using-VxLan-overlay-network/assets/53372696/f40301cd-a041-4b8e-ad64-03957a5601bd">

What is vxlan?

In modern network topologies, virtualized Layer 2 networks are built on top of the underlying physical infrastructure known as the underlay network using overlay networks like VXLAN. VXLAN uses the VNI, a 24-bit identifier, to distinguish between distinct virtual networks within the overlay. VTEPs act as gateways between the overlay and underlay networks by encapsulating and decapsulating the VXLAN packets. Overlay networks using VXLAN, VNI, and VTEP technologies make it possible for flexible provisioning, multi-tenancy, and isolation while also enabling flexible network topologies that are scalable and adaptable in virtualized and cloud settings.
