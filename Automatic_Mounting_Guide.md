# Automatic Mounting of `/dev/sdb1` to `/data` Guide

Our goal is to mount `/dev/sdb1` to the `/data` directory every time the system boots up. This guide will walk you through identifying the drive, creating a mount point, and setting it to auto-mount at boot.

## 1. Verifying the Device

Before making any configurations, let's ensure `/dev/sdb1` is the correct partition.

To verify:

```bash
lsblk /dev/sdb
```

## 2. Setting Up the Mount Point

If `/data` directory doesn't already exist, we'll create it.

To set up the mount point:

```bash
sudo mkdir -p /data
```

## 3. Retrieving the UUID

Using UUIDs ensures consistent mounting even if device names change. 

To retrieve the UUID of `/dev/sdb1`:

```bash
sudo blkid /dev/sdb1
```

Note the UUID for the next steps.

## 4. Configuring `/etc/fstab`

We'll be editing `/etc/fstab` to ensure our partition is mounted at boot.

To make the configuration:

```bash
sudo vim /etc/fstab
```

Then, append the following line, replacing `YOUR_UUID_HERE` with the UUID from the previous step:

```
UUID=YOUR_UUID_HERE   /data   ext4    defaults    0   2
```

**Note**: Replace `ext4` with your actual filesystem if it's different. To determine the filesystem of a specific partition like `/dev/sdb1`, you can use the `lsblk` command with the `-f` flag, which stands for "filesystem":
```bash
lsblk -f /dev/sdb1
```

## 5. Testing the Configuration

Let's ensure our changes work as expected.

To test:

```bash
sudo mount -a
```

Then, confirm the mount:

```bash
df -h | grep /data
```

## 6. Finalizing

A quick reboot will confirm if our setup works as intended.

To reboot:

```bash
sudo reboot
```

After the system restarts, `/dev/sdb1` should be automatically mounted to `/data`.

