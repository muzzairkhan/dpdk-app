# dpdk-app
A simple DPDK Layer 2 (Ethernet) forwarder application written in C language which
- Parse incoming packets to extract basic Ethernet/IP header information.
- Drop packets if they are non-IPv4 packets.
- modify the packet headers (e.g., change the destination MAC address).
- Forward valid packets to the appropriate interface.
- Provide console output for packet statistics.
- Implement logging for debugging purposes.
Please find the detail guide of this application here: https://doc.dpdk.org/guides/sample_app_ug/l2_forward_real_virtual.html

# Environment Setup
1. VirtualBox with Ubuntu 24.04 Guest (4GB RAM, 4 Processor Cores, 40 GB Storage space and 3 Network Interfaces)
2. Fetch the dpdk source: git clone https://github.com/DPDK/dpdk
3. Follow the dependencies setup guide at https://doc.dpdk.org/guides/linux_gsg/sys_reqs.html#compilation-of-the-dpdk
   It mainly requires build-essentials, Python 3.6 or later, meson, Ninja, pyelftools, libnuma-dev
4. Huge Pages allocation for packet buffer memory pools (reserved 64 pages of size 2 MB)
   - "sudo chmod 777 /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages"
   - "echo 64 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages"
5. Loading dpdk NIC drivers in kernel and configuring the two (out of 3) network interfaces for dpdk
   - "sudo modprobe vfio-pci enable_unsafe_noiommu_mode=1"
   - "dpdk-devbind.py --noiommu-mode --bind=vfio-pci enp0s8"
   - "dpdk-devbind.py --noiommu-mode --bind=vfio-pci enp0s9" where enp0s8 enp0s9 are the two network interfaces to be configured with dpdk.
   - To check current status of Ethernet devices:
   "dpdk/user-tools/dpdk-devbind.py --status"

# How to Compile and Run the application
1. Compile command:
   - "sudo meson setup -Dexamples=l2fwd dpdk build"
   where "l2fwd" is the example dpdk application, "dpdk" is the dpdk source directory and "build" is the output dir.
   - "sudo meson compile -C build" 
2. To run the application: 
   - "cd /build/examples"
   - "sudo ./dpdk-l2fwd -l 0-1 -n 4 -- -q 2 -p 3"
where we specified the first two processor cores, 4 memry channels, 2 queues per processor core, and 2 ethernet ports
 
