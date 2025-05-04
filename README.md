# 🛠️ Linux LVM Setup and Resize — Action Summary
---
### 📋 **Overview**
This document summarizes the successful setup, formatting, mounting, and resizing of a new logical volume (`caleston_vg-data`) on a Linux system by Jaffy.
---
### 🔍 **Step-by-Step Journey**
---
#### 1️⃣ **Initial LVM and PV Checks**
- Jaffy ran:
  ```bash
  lvs
  pvdisplay
  ```
  ⚠️ Got permission errors as a non-root user.
- Retried with `sudo`:
  ```bash
  sudo lvs
  sudo pvdisplay
  ```
  ✅ Confirmed:
  - Volume Group: `vagrant-vg`
  - Logical Volumes: `root` (<9.04 GiB), `swap_1` (980 MiB)
  - Physical Volume: `/dev/vda1` (fully allocated)
---
#### 2️⃣ **Creating a New Volume Group**
- Attempted to create:
  ```bash
  sudo vgcreate -f caleston_vg /dev/vdb /dev/vdc
  ```
  ⚠️ **Failed**: `caleston_vg` already existed.
- Fixed by removing and recreating:
  ```bash
  sudo vgremove caleston_vg
  sudo vgcreate -f caleston_vg /dev/vdb /dev/vdc
  ```
  ✅ **Volume group successfully created**
---
#### 3️⃣ **Creating and Formatting Logical Volume**
- Created logical volume:
  ```bash
  sudo lvcreate -L 1G -n data caleston_vg
  ```
- Made filesystem (after correcting typos like `mfks.ext4`):
  ```bash
  sudo mkfs.ext4 /dev/mapper/caleston_vg-data
  ```
- Prepared mount point (already existed):
  ```bash
  sudo mkdir /mnt/media
  ```
- Mounted:
  ```bash
  sudo mount /dev/mapper/caleston_vg-data /mnt/media/
  ```
✅ **Logical volume mounted and ready**
---
#### 4️⃣ **Resizing Logical Volume and Filesystem**
- Increased size by 50 MiB:
  ```bash
  sudo lvresize -L +50M /dev/mapper/caleston_vg-data
  ```
- Resized the filesystem online:
  ```bash
  sudo resize2fs /dev/mapper/caleston_vg-data
  ```
✅ **Filesystem successfully resized** — now using:
> 390,144 (4k) blocks
---
### 🎉 **Final Status**
| **Item**             | **Status**                 |
|----------------------|---------------------------|
| Volume Group         | `caleston_vg` created     |
| Logical Volume       | `data` created, resized   |
| Mount Point          | `/mnt/media` mounted      |
| Filesystem Type      | ext4                     |
| Total Size (after resize) | ~1.49 GiB             |
---
### 💡 **Next Steps**
- Validate mount in `/etc/fstab` for persistence.
- Monitor available space using `df -h`.
- Set appropriate permissions on `/mnt/media`.
![image](https://github.com/user-attachments/assets/71b5a34e-f6a5-4ced-bec6-f695b568ed3b)
