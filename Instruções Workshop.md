# Fist demo - Full Disk Encryption on local VMs through QEMU + KVM

## Test environment

- Host OS: Fedora 42 Workstation (Linux 6.18)
- TestVM OS: Ubuntu

## Setting up QEMU + VirtManager

Install the required packages with the command below:

```bash
sudo dnf install @virtualization # installs VirtManager and QEMU
```

After that, enable the virtualization service `libvirtd`:

```bash
sudo systemctl enable --now libvirtd
```

Optionally, add the current user to the `libvirt` group to
avoid typing `sudo` every time.

```bash
sudo usermod -aG libvirt $(whoami)
```

## Accessing the image disk file

After setting up the local VM as you wish, then

We'll use `guestmount` to mount the VM disk file into our host's
filesystem, so we can access the files created during our tests.

```bash
guestmount -a <path/of/file.qcow2> -m <guest_partition> --ro <target_dir>
```

After verifying that the test files are present, we unmount the
image file using the following command:

```bash
guestunmount <target_dir>
```

## Encrypting the image with QEMU

Use the following command to create an encrypted version of the image:

```bash
qemu-img convert -f qcow2 -O qcow2 --object secret,id=sec0,data=<password> \
    -o encrypt.format=luks,encrypt.key-secret=sec0 \
    <path/to/source-image.qcow2> <path/to/output-image.qcow2>
```

Once finished, you can verify the state of the new image file with:

```bash
qemu-img info <path/to/output-image.qcow2>
```

The command above should yield something as the following:

```bash
# Output example
# ...
file format: qcow2
virtual size: 25 GiB (26843545600 bytes)
disk size: 8.64 GiB
encrypted: yes # image is encrypted
cluster_size: 65536
Format specific information:
    compat: 1.1
    compression type: zlib
    lazy refcounts: false
    refcount bits: 16
    encrypt:
        ivgen alg: plain64
        detached header: false
        hash alg: sha256
        cipher alg: aes-256
        uuid: 7a80f591-517c-45c6-b25a-5c7962a74555
        format: luks # encrypted using LUKS
        cipher mode: xts # encrypted with AES-XTS
        slots:
        # ...
    file length: 8.64 GiB (9278717952 bytes)
    disk size: 8.64 GiB
```

## Checking image encryption

You can manually verify if the disk image is, indeed, encrypted by
checking the raw contents of the QCOW2 files.

Run the following command against the encrypted image and the original
one:

```bash
head -n 50 <path/to/image.qcow2>
```

# Second demo

## Test environment

- Cloud Provider: AWS
- Host OS: EC2 Instance (Default configurations)
- TestVM OS: Ubuntu Server
- Tooling: cryptsetup (LUKS)

## Setting up the EC2 Instance & disk

- Launch the EC2 Instance: Navigate to the Amazon EC2 Dashboard and create a new instance. Select Ubuntu as the Amazon Machine Image (AMI) instead of the default Amazon Linux. Default instance types (like `t2.micro`) are sufficient for this demo.
- Create and Attach a Volume: Navigate to Elastic Block Store (EBS) > Volumes. Create a new EBS volume in the same Availability Zone as your instance. Select the volume, choose Attach volume, and attach it to your Ubuntu EC2 instance.

## Encrypting and mounting the disk

Once your instance is running and the volume is attached, SSH into your Ubuntu VM.

### Step 1: Identify the newly attached disk

Run the following command to list all available block devices. Look for the unformatted disk you just attached (often named `/dev/sdb`, `/dev/xvdf`, or `/dev/nvme1n1` depending on the instance type).

```bash
lsblk
```

### Step 2: Install the encryption tool

Install `cryptsetup`, which provides the utilities needed to configure LUKS encryption:

```bash
sudo apt update && sudo apt install cryptsetup -y
```

### Step 3: Initialize the LUKS partition

This step will erase any existing data on the disk. Before you can open and use the disk, you must format it as a LUKS encrypted partition. You will be prompted to confirm (type YES in all caps) and to set a secure passphrase.

Assuming the disk is `/dev/sdb`, run the following command.

```bash
sudo cryptsetup luksFormat /dev/sdb
```

### Step 4: Open the encrypted disk

Now, map the encrypted physical disk to a virtual device mapper. Replace `<encrypted_disk_name>` with a descriptive name of your choice. You will need to enter the passphrase you created in Step 3. This creates a decrypted mapping located at `/dev/mapper/<encrypted_disk_name>`

```bash
sudo cryptsetup open /dev/sdb <encrypted_disk_name>
```

### Step 5: Format the mapped volume

Create a filesystem on the new decrypted mapping. We will use the ext4 filesystem for this demo.

```bash
sudo mkfs.ext4 /dev/mapper/<encrypted_disk_name>
```

### Step 6: Mount the encrypted disk

Finally, create a directory to serve as the mount point and mount the decrypted mapping to it.

Finally, run

```bash
sudo mkdir /mnt/<mount_point>
sudo mount /dev/mapper/<encrypted_disk_name> /mnt/<mount_point> 
```

Verify that the disk is successfully mounted and available by running:

```bash
df -h
```