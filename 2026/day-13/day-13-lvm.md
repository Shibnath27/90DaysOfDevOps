# ✅ Day 13 – Linux Volume Management (LVM)

> Do this on a **VM / cloud instance only**.
> You already know the basics — now we manage storage *properly*.

---

## 🔹 Before You Start

Switch to root:

```bash
sudo -i
```

### If you DON’T have a spare disk (most common case)

Create a virtual disk using a loop device:

```bash
dd if=/dev/zero of=/tmp/disk1.img bs=1M count=1024
losetup -fP /tmp/disk1.img
losetup -a
```

Note the loop device (example: `/dev/loop0`).

👉 I’ll assume **`/dev/loop0`** below
(Replace with your actual device if different.)

---

## 🔹 Task 1: Check Current Storage

```bash
lsblk
pvs
vgs
lvs
df -h
```

📌 Observation:

* No PV/VG/LV exists initially (clean system)

📸 Screenshot this output

<img width="847" height="491" alt="image" src="https://github.com/user-attachments/assets/62e76316-ca56-45e3-90d4-a4e67bc81c3b" />

---

## 🔹 Task 2: Create Physical Volume (PV)

```bash
pvcreate /dev/nvme1n1 /dev/nvme2n1
pvs
```

✔ Now `pvcreate /dev/nvme1n1 /dev/nvme2n1` is a Physical Volume

📸 Screenshot `pvs`

<img width="488" height="128" alt="image" src="https://github.com/user-attachments/assets/e4192eab-ffe4-4d8e-a30f-f2853ef04616" />

---

## 🔹 Task 3: Create Volume Group (VG)

```bash
vgcreate devops-vg /dev/nvme1n1 /dev/nvme2n1
vgs
```

✔ Volume Group `devops-vg` created

📸 Screenshot `vgs`

<img width="576" height="102" alt="image" src="https://github.com/user-attachments/assets/dc7b352c-ba40-43d6-a825-e70e4d7f43b2" />

---

## 🔹 Task 4: Create Logical Volume (LV)

```bash
lvcreate -L 10G -n app-data devops-vg
lvs
```

✔ Logical Volume created:
`/dev/devops-vg/app-data`

📸 Screenshot `lvs`

<img width="741" height="66" alt="image" src="https://github.com/user-attachments/assets/1506fc3a-7ec7-4158-b3b7-7e593cf72dac" />

---

## 🔹 Task 5: Format and Mount the Volume

### Format

```bash
mkfs.ext4 /dev/devops-vg/app-data
```

<img width="614" height="201" alt="image" src="https://github.com/user-attachments/assets/0aeb8829-7754-4587-8368-68e66ed97d32" />


### Mount

```bash
mkdir -p /mnt/app-data
mount /dev/devops-vg/app-data /mnt/app-data
df -h /mnt/app-data
```

✔ Volume mounted successfully

📸 Screenshot `df -h /mnt/app-data`

<img width="595" height="49" alt="image" src="https://github.com/user-attachments/assets/3ce94052-8e36-452e-8994-e024c9f3ac0e" />

---

## 🔹 Task 6: Extend the Volume

### Extend Logical Volume

```bash
lvextend -L +5G /dev/devops-vg/app-data
```

### Resize filesystem

```bash
resize2fs /dev/devops-vg/app-data
```

### Verify

```bash
df -h /mnt/app-data
```

✔ Storage increased without unmounting

📸 Screenshot final size

<img width="768" height="158" alt="image" src="https://github.com/user-attachments/assets/0c3110da-0263-4684-887c-08ee484b120d" />

---

# 📄 `day-13-lvm.md` (FINAL – Copy/Paste)

```markdown
# Day 13 – Linux Volume Management (LVM)

## Commands Used
- lsblk
- pvs, vgs, lvs
- pvcreate
- vgcreate
- lvcreate
- mkfs.ext4
- mount
- lvextend
- resize2fs
- df -h

## Storage Setup
- Physical Volume: /dev/nvme1n1 /dev/nvme2n1
- Volume Group: devops-vg
- Logical Volume: app-data
- Mount Point: /mnt/app-data

## Screenshots
- Initial storage check
- PV creation
- VG creation
- LV creation
- Mounted volume
- Extended volume size

## What I Learned
- LVM allows flexible disk management without downtime
- Logical volumes can be extended online safely
- LVM is critical for production servers and cloud instances
```

