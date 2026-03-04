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

# Second demo - TODO