# Multi-container-host-networking-using-VxLan-overlay-network
Environment setup: At first you have to take two virtual machine and virtual machine should ping one another. I am using centos9 for this demo and I have taken two Centos9 virtual machines from ec2 instances. For Ec2 security group I am allowing all traffic to cope up with connectivity problem. Two virtual machine should be in same vpc so that you can ping one from another.Here is the security group picture


<img width="713" alt="security group" src="https://github.com/nobelrakib/Multi-container-host-networking-using-VxLan-overlay-network/assets/53372696/3c7c55d0-5299-4a26-842d-995a45daf819">

What we are going to make

<img width="469" alt="overlay" src="https://github.com/nobelrakib/Multi-container-host-networking-using-VxLan-overlay-network/assets/53372696/f40301cd-a041-4b8e-ad64-03957a5601bd">

What is vxlan?

In modern network topologies, virtualized Layer 2 networks are built on top of the underlying physical infrastructure known as the underlay network using overlay networks like VXLAN. VXLAN uses the VNI, a 24-bit identifier, to distinguish between distinct virtual networks within the overlay. VTEPs act as gateways between the overlay and underlay networks by encapsulating and decapsulating the VXLAN packets. Overlay networks using VXLAN, VNI, and VTEP technologies make it possible for flexible provisioning, multi-tenancy, and isolation while also enabling flexible network topologies that are scalable and adaptable in virtualized and cloud settings.

Let’s create a overlay network which will connect two docker container in two diffrent virtual machine.

For virtual machine one follow these steps

```
#log in using key
1.ssh -i vxlan.pem ec2-user@ec2-3-113-32-164.ap-northeast-1.compute.amazonaws.com
#update repository
2.yum update -y
#install docker
3.sudo yum install -y yum-utils
4.sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
5.sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
#start docker
6.sudo systemctl start docker
#create a subnet by docker network
7.sudo docker network create --subnet 172.18.0.0/16 vxlan-net
#check a new interface has created
8.ip a
  br-ee33ea6734a1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:5f:f4:d7:b6 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-ee33ea6734a1
       valid_lft forever preferred_lft forever
    inet6 fe80::42:5fff:fef4:d7b6/64 scope link
       valid_lft forever preferred_lft forever
#Now create a docker container by our newly created subnet and assign static ip address to it
9.sudo docker run -d --net vxlan-net --ip 172.18.0.11 ubuntu sleep 3000
#check container list
10.docker ps
CONTAINER ID   IMAGE     COMMAND        CREATED       STATUS         PORTS     NAMES
4ac2f4b8a2f4   ubuntu    "sleep 3000"   5 hours ago   Up 9 seconds             cool_austin
#now go inside docker container and install necessary dependencies
11.sudo docker exec -it 4a bash
12.apt-get update
13.apt-get install net-tools
14.apt-get install iputils-ping
15.exit
#now we have to create vxlan and one side of it will connected with host virtual machine 
#eth0 interface and anothe end is connected with our docker bridge interface
#named br-ee33ea6734a1 so that when we ping from docker it first come to docker 
#bridge then eth0 interface and then throug tunneling it goes to another virtual machine
16.sudo ip link add vxlan-demo type vxlan id 100 remote 172.31.37.176 dstport 4789 dev eth0
#here 172.31.37.176 is another virtual machine ip
#now a new interface will created name vxlan-demo in host machine which is
#initially down you have to up it and attach it to 
17.sudo ip link set vxlan-demo up
18.sudo ip link set vxlan-demo master br-ee33ea6734a1
#now virtual machine one is ready
#lets set up the virtual machine two
```
