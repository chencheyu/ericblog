---
date: 2024-10-16
publish: true
slug: 6fb417d7-7230-4747-ad4d-01f7e55efcea
tags:
- CS/Linux
- CS/VirtualMachine
title: Virtual Machine
uuid: 9dca1f42-0915-4d1e-ab49-1ebf0107455c
---
### qemu-user

### qemu-system

### virt-manager

libvirt 的 GUI

### [libvirt](https://libvirt.org/)

`kvm-ok` 用於檢察 CPU 是否支援虛擬化
[libvirt: Domain XML format](https://libvirt.org/formatdomain.html)

#### virtiofsd

qemu 再做 filesystem share 時沒裝 virtiofsd 啟動虛擬機時會報失敗
virt-manager 中 Add Hardware 選 filesystem
Source Path 選在 host  要往 guest share 的路徑
Target Path 是 virtiofs 在 guest 中要打的 Tag，不建議有斜線

```sh
mount -t virtiofs share /mnt/share
```

在 guest 上 mount Share Filesystem

### qemu-guest-agent

qemu guest 建議安裝的軟體用於快照，有教學要求 systemctl enable 但我在 ubuntu 24.04 上測試惠自動 enable

### Log

- `$HOME/.virtinst/virt-install.log` – virt-install tool log file.
- `$HOME/.virt-manager/virt-manager.log` – virt-manager tool log file.
- `/var/log/libvirt/qemu/` – Log files for each running virtual machine. If centos is virtual machine name, than log file is `/var/log/libvirt/qemu/centos.log`

### Virtsh

libvirtd 的命令行界面

```sh
virsh list
```

列出正在運行的虛擬機器

```sh
virsh start <guestname>
```

要啟動虛擬機器

```sh

```

### [Spice](https://spice-space.org/)

Simple Protocol for Independent Computing Environments
用以共享視訊、錄製音訊、共享 USB 裝置和共用資料夾
SPICE 專案旨在提供完整的開源解決方案，以無縫方式遠端存取虛擬機器
需要 SPICE 用戶端才能連線到來賓。在 Arch 中，可以使用下列客戶端：

- virt-viewer
- spice-vdagent 作為 ubuntu24 的 spice 客戶端程式
  客機的 VGA 可以從 SPICE 傳出

### [Virtio-FPGA - a virtualization solution for SoC-attached FPGAs](https://arxiv.org/abs/2304.01721)

> 最近，FPGA 加速器越來越受歡迎，因為它們提供了一種滿足即時應用的高運算和低功耗需求的合適方法。現代電動交通系統（例如飛機、道路車輛）可以從嵌入式 FPGA 中獲益匪淺，它將高性能和靈活性功能整合到單一 SoC 中。同時，FPGA資源的虛擬化旨在增強這些系統的強大隔離性、整合性和安全性。在本文中，我們提出了一種新穎的虛擬化框架，針對 Linux 和 QEMU/KVM 設定中的 SoC 連接的 FPGA 設備。我們使用 Virtio 作為一種有效方式從客戶系統配置 FPGA 資源的方法。此外，我們也採用 Linux VFIO 和裝置樹覆蓋技術，以便為客戶系統提供動態可存取的 FPGA 資源。詳細描述了從虛擬化環境動態配置和利用 FPGA 資源的能力。介紹了此解決方案的評估流程，並且從客戶系統存取 FPGA 設備時，虛擬化開銷的基準測試為最低（約 10%）
