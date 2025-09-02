our administrator has establihed mount points on a linux virtual machine that I use. I would like to replicate those mount points on a separate linux system. exactly what commands do I need to execute to create those mount points on the separate system. Here are the two mount points I want to replicate:

/mnt/PAM_Archive                    thalassa.shore.mbari.org:/PAM_Archive
│                                                      nfs4       ro,relatime,vers=4.1,rsize=1048576,wsize=1048576,naml

/mnt/PAM_Analysis                   thalassa.shore.mbari.org:/PAM_Analysis
│                                                      nfs4       rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,naml

To replicate these NFS mount points on your separate Linux system, you'll need to execute several commands. Here's the complete process:

1. Install NFS client utilities (if not already installed)

bash
# For Debian/Ubuntu systems:
sudo apt update

sudo apt install nfs-common

# For RHEL/CentOS/Fedora systems:
sudo yum install nfs-utils
# or

sudo dnf install nfs-utils

2. Create the mount point directories

bash

sudo mkdir -p /mnt/PAM_Archive

sudo mkdir -p /mnt/PAM_Analysis

3. Create entries in /etc/fstab for permanent mounting

Edit the fstab file:

bash

sudo nano /etc/fstab

Add these lines at the end of the file:

text

thalassa.shore.mbari.org:/PAM_Archive    /mnt/PAM_Archive    nfs4    ro,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255    0 0

thalassa.shore.mbari.org:/PAM_Analysis   /mnt/PAM_Analysis   nfs4    rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255    0 0

4. Mount the shares immediately (without reboot)

bash

# Mount both shares

sudo mount -a

# Or mount them individually:

sudo mount /mnt/PAM_Archive

sudo mount /mnt/PAM_Analysis

5. Verify the mounts are working
bash

# Check if mounts are active

mount | grep PAM

# Check disk space usage

df -h /mnt/PAM_Archive /mnt/PAM_Analysis

# Test read access (for the read-only mount)
ls -la /mnt/PAM_Archive/

# Test write access (for the read-write mount)
touch /mnt/PAM_Analysis/test_file && rm /mnt/PAM_Analysis/test_file

Alternative: Manual mounting (temporary)

If you only want to test or temporarily mount the shares:

bash
# Mount temporarily (won't survive reboot)

sudo mount -t nfs4 -o ro,relatime,vers=4.1,rsize=1048576,wsize=1048576 thalassa.shore.mbari.org:/PAM_Archive /mnt/PAM_Archive

sudo mount -t nfs4 -o rw,relatime,vers=4.1,rsize=1048576,wsize=1048576 thalassa.shore.mbari.org:/PAM_Analysis /mnt/PAM_Analysis
Important Notes:

Network Connectivity: Ensure your system can reach thalassa.shore.mbari.org

Permissions: You may need appropriate permissions on the NFS server

Firewall: Check that NFS ports (2049) are not blocked

Name Resolution: The hostname must be resolvable via DNS or /etc/hosts

The /etc/fstab method is recommended for permanent mounts that will survive reboots.
