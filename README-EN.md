# Instituto Superior Técnico, Universidade de Lisboa
**Computer Networks and Systems Security**
# Configuration Guide - *Kathará*
## Objective
The objective of this guide is to introduce *[Kathará](https://www.kathara.org/)*, describe how to install it, and verify its basic functionality.
## What is Kathará
*Kathará* is a tool that allows the simulation of computer networks and will be used as the foundation for laboratories and projects.
We will use Kathará to simulate networks and compare alternative protocols and configurations.
In short, Kathará allows the creation of multiple machines - implemented as *[Docker](https://docs.docker.com/)* containers - and enables the configuration of how they are connected in collision domains, that is, points in the simulated network where packets reach all computers through broadcast, which are implemented by *[Docker Bridge Networks](https://docs.docker.com/network/bridge/)*.
This video presents an introduction to Kathará:
<https://www.youtube.com/watch?v=ionEpKjv3Vk>
## Installing Kathará
Kathará is installed on the laboratory computers running Ubuntu GNU/Linux.
To access the laboratory computers, use the username "seed" and the password "dees".
To install Kathará on your PC, download the "Latest Stable Release" from
> <https://www.kathara.org/#download>
and follow the steps indicated at
> https://github.com/KatharaFramework/Kathara/wiki
## Basic Test
The purpose of this basic test is to verify that Kathará is correctly installed.
We will create a network with 2 computers physically connected to the same *switch* and confirm that they can "ping" each other.
1. Clone this git repository to your machine
```bash
git clone 'git@github.com:tecnico-sec/Kathara-Setup.git'
```
2. Examine the content of the `lab.conf` file to understand why the network between the 2 PCs is generated.
3. Run the laboratory by changing to the directory that contains the *lab.conf* file and executing the `lstart` command (`kathara lstart`).
Observe the startup of the two Docker containers.
Verify that the bash scripts `pc1.startup` and `pc2.startup` are executed after their respective machines start.
4. On each machine, obtain its IP address within the 1.2.0.0/24 network.
```bash
ip addr
```
| Machine  | IP address    |
| -------- | ------------- |
| pc1      |               |
| pc2      |               |
5. "Ping" the machines mutually.
```bash
# example: on pc1
ping 1.2.0.2
# ctrl+c to stop the command
```
6. Close the laboratory by executing the `lclean` command (`kathara lclean`).
This will delete the content of the machines and stop running them.
# Capturing and Observing Traffic Between Machines
Now we will proceed to capture the traffic of a ping between machines from the perspective of the packets that arrive at the network interface of each machine and subsequently observe it on the host.
1. In the folder where the `lab.conf` file is located, the *shared* folder is a persistent folder shared between all machines and the host. We will use it to dump the network traffic that reaches the network interface of each PC.
2. To do this, we will start by creating this folder on the host.
```bash
# Inside the folder containing lab.conf:
mkdir shared
```
2. Start the laboratory:
```bash
lstart
```
3. Execute the [*tcpdump*][7] command on the respective machines to start capturing the traffic that arrives at the `eth0` interface within the respective files in the *shared* folder:
```bash
# pc1:
tcpdump -s 0 -i eth0 -U -w /shared/pc1.pcap &
# pc2:
tcpdump -s 0 -i eth0 -U -w /shared/pc2.pcap &
```
4. On machine pc1, "ping" machine pc2 and then "ping" an address that does not exist:
```bash
# pc1:
ping -c 1 1.2.0.2
ping -c 1 1.2.0.3
```
5. Stop the laboratories by executing `lclean` on the host PC. Wait a moment to ensure that the files `pc1.pcap` and `pc2.pcap` are saved (you can check if they have more than 0 bytes in the `shared` folder).
6. Run the [*Wireshark*][8] program and open one of the packets to observe the content of the ping between pc1 and pc2 from the perspective of the packets that reach pc1 and pc2 when they are connected in the same collision domain of *Kathará*.
7. What physical network component would have the same behavior that *Kathará* simulates in a collision domain?
## References
-   Kathará, [https://github.com/KatharaFramework/Kathara/wiki/][3]
-   Docker documentation, [https://docs.docker.com/][6]
-   man ip
-   man tcpdump, [https://www.tcpdump.org/manpages/tcpdump.1.html][7]
-  Wireshark, [https://www.wireshark.org/][8]
  [3]: https://github.com/KatharaFramework/Kathara/wiki
  [6]: https://docs.docker.com/
  [7]: https://www.tcpdump.org/manpages/tcpdump.1.html
  [8]: https://www.wireshark.org/
