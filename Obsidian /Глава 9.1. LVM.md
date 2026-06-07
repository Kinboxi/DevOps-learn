# LVM (Logical Volume Manager)

LVM — это инструмент для гибкого управления дисками в Linux. Главный плюс — разделы можно расширять на лету, не останавливая систему.

---

## Философия

Без LVM разделы фиксированы — создал 100 ГБ, больше не станет.  
С LVM — добавил диск, расширил раздел за минуту, сервер не останавливал.

**Главное правило:** LVM ставят заранее, при установке системы. Системные разделы (корень `/`) в LVM не добавляют — только новые чистые диски.

---

## Три уровня

```
Диск → PV → VG → LV → файлы
```

| Уровень | Расшифровка | Что это |
|---|---|---|
| PV | Physical Volume | Реальный диск отданный LVM |
| VG | Volume Group | Пул из нескольких PV |
| LV | Logical Volume | Раздел нарезанный из пула |

**Аналогия:**
- PV — склады (физические диски)
- VG — общий учёт всех складов
- LV — выделенная секция под конкретный товар

---

## Базовый цикл

### 1. Создать PV
```bash
sudo pvcreate /dev/sdb
```
Регистрирует диск в LVM. Без этого LVM его не видит.

### 2. Создать VG
```bash
sudo vgcreate my_vg /dev/sdb
```
Создаёт пул из одного или нескольких PV.

### 3. Создать LV
```bash
sudo lvcreate -L 100G -n data my_vg
```
Нарезает раздел 100 ГБ из пула `my_vg`, называет его `data`.

### 4. Отформатировать
```bash
sudo mkfs.ext4 /dev/my_vg/data
```
Создаёт файловую систему на томе.

### 5. Смонтировать
```bash
sudo mkdir /mnt/data
sudo mount /dev/my_vg/data /mnt/data
```

---

## Просмотр

```bash
pvs        # список физических томов
vgs        # список групп
lvs        # список логических томов
lsblk      # дерево дисков и разделов
df -h      # занятое/свободное место
```

---

## Расширение тома

Когда место на томе заканчивается:

```bash
# Если в группе есть свободное место:
sudo lvextend -L +50G /dev/my_vg/data
sudo resize2fs /dev/my_vg/data

# Если группа забита — сначала добавить новый диск:
sudo pvcreate /dev/sdc
sudo vgextend my_vg /dev/sdc
sudo lvextend -L +50G /dev/my_vg/data
sudo resize2fs /dev/my_vg/data
```

> `lvextend` расширяет том, `resize2fs` говорит файловой системе что места стало больше. Нужны обе команды.

---

## Снапшоты

Снапшот — мгновенный снимок состояния тома. Удобно делать перед обновлениями.

```bash
# Создать снапшот
sudo lvcreate -L 1G -s -n data_snap /dev/my_vg/data

# Откатиться к снапшоту
sudo lvconvert --merge /dev/my_vg/data_snap

# Удалить снапшот
sudo lvremove /dev/my_vg/data_snap
```

---

## Удаление

```bash
sudo umount /mnt/data
sudo lvremove /dev/my_vg/data
sudo vgremove my_vg
sudo pvremove /dev/sdb
```

Удалять в обратном порядке: сначала LV, потом VG, потом PV.

---

## Тестовая среда (loopback)

Если нет лишнего диска — можно потренироваться на виртуальных:

```bash
# Создать виртуальные диски
dd if=/dev/zero of=/tmp/disk1.img bs=1M count=500
dd if=/dev/zero of=/tmp/disk2.img bs=1M count=500

# Подключить как блочные устройства
sudo losetup -f --show /tmp/disk1.img
sudo losetup -f --show /tmp/disk2.img

# Отключить после работы
sudo losetup -d /dev/loop0
sudo losetup -d /dev/loop1
```
