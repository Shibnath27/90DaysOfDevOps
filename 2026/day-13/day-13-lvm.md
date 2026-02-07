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

---

## 🔹 Task 2: Create Physical Volume (PV)

```bash
pvcreate /dev/loop0
pvs
```

✔ Now `/dev/loop0` is a Physical Volume

📸 Screenshot `pvs`

---

## 🔹 Task 3: Create Volume Group (VG)

```bash
vgcreate devops-vg /dev/loop0
vgs
```

✔ Volume Group `devops-vg` created

📸 Screenshot `vgs`

---

## 🔹 Task 4: Create Logical Volume (LV)

```bash
lvcreate -L 500M -n app-data devops-vg
lvs
```

✔ Logical Volume created:
`/dev/devops-vg/app-data`

📸 Screenshot `lvs`

---

## 🔹 Task 5: Format and Mount the Volume

### Format

```bash
mkfs.ext4 /dev/devops-vg/app-data
```

### Mount

```bash
mkdir -p /mnt/app-data
mount /dev/devops-vg/app-data /mnt/app-data
df -h /mnt/app-data
```

✔ Volume mounted successfully

📸 Screenshot `df -h /mnt/app-data`

---

## 🔹 Task 6: Extend the Volume

### Extend Logical Volume

```bash
lvextend -L +200M /dev/devops-vg/app-data
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
- Physical Volume: /dev/loop0
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

