![Đồ án Docker NT132 pptx](https://github.com/user-attachments/assets/12846b49-1169-4a1c-ba6d-86b3f628323f)

### OpenNebula On-Premise Deployment Guide
This project provides step-by-step instructions to deploy a private cloud infrastructure using OpenNebula with KVM on Ubuntu Server.

### Prerequisites
- Ubuntu Server 20.04 LTS or later
- Root or sudo privileges
- Minimum 2 machines (1 Frontend, 1 or more Compute Nodes)
- Basic networking knowledge
- Internet access for downloading packages

### Technologies Used
- OpenNebula
- KVM (Kernel-based Virtual Machine)
- NFS (for shared storage)
- Bash scripting
- Sunstone (Web UI)

### Deployment Steps
1. Update System
   ```
   sudo apt update && sudo apt upgrade -y
   ```

2. Install KVM and Dependencies
   ```
   sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager -y
   ```
3. Add OpenNebula Repository
   ```
   wget -q -O- https://downloads.opennebula.io/repo/repo.key | sudo apt-key add -
   ```
   ```
   echo "deb https://downloads.opennebula.io/repo/6.6/Ubuntu/20.04 stable opennebula" | sudo tee /etc/apt/sources.list.d/opennebula.list
   ```
   ```
   sudo apt update
   ```
4.  Install OpenNebula Frontend (On Master Node)
    ```
    sudo apt install opennebula opennebula-sunstone opennebula-gate opennebula-flow -y
    ```
    
5.  Start and Enable OpenNebula Services
    ```
     sudo systemctl enable opennebula opennebula-sunstone
     sudo systemctl start opennebula opennebula-sunstone
    ```
    
6.  Configure Sunstone Web UI
    - Access it via:
      ```
      http://<frontend-ip>:9869
      ```
    - Login with:
      - User: oneadmin
      - Password: (from /var/lib/one/.one/one_auth)
7.  Set up Compute Node(s)
    - Install KVM and OpenNebula node packages:
     
       ```
       sudo apt install opennebula-node -y
       ```
       
    - Set up passwordless SSH from frontend to compute node:
    
        ```
        ssh-keygen
        ssh-copy-id oneadmin@compute-node-ip
        ```
      
8.   Configure NFS Shared Storage
  
9.  Add Host in Sunstone UI

      - Go to Infrastructure → Hosts → Add Host and input the compute node IP. Choose KVM as the hypervisor.

10. Upload ISO and Create Templates
      - Use the Sunstone UI to upload an ISO file (e.g. Ubuntu image), and create a virtual machine template for deployment.
      
11. Configure Network Bridge on Compute Node(s)
      - Create Bridge br0 on Ubuntu
      - Edit Netplan configuration:
        ```
        sudo nano /etc/netplan/01-netcfg.yaml
        ```

        ```
        network:
        version: 2
        renderer: networkd
        ethernets:
          ens33:
          dhcp4: no
        bridges:
          br0:
          interfaces: [ens33]
          dhcp4: yes
        ```
      - Apply changes:
        ```
        sudo netplan apply
        ```
  12. Create Virtual Network in OpenNebula (VNet)
    
  13. Create Virtual Machine.
