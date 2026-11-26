# stock/ —— 原厂镜像（以 zip 存放）

`07-mkbootimg.sh` 需要**你自己设备**的原厂 boot 镜像作为模板。原厂
`boot_a.img` 为 128 MB（超过 GitHub 单文件 100 MB 限制），故以 **zip 压缩**
形式放在本目录：

```
stock/
├── boot_a.zip   # 必需：原厂 boot_a.img（header v2 模板：kernel/ramdisk
│                #   加载地址、cmdline、dt-table 段），CI workflow 会解压使用
└── dtbo_a.zip   # 可选：原厂 dtbo_a.img（恢复原厂 / 刷机参考）
```

本地构建 `make bootimg` 前先解压出 `stock/boot_a.img`（也可放入未压缩的
`boot_a.img`，脚本/workflow 均优先使用已解压文件）：

```bash
unzip -o -d stock/ stock/boot_a.zip
unzip -o -d stock/ stock/dtbo_a.zip   # 需要时
```

备份方法（设备已 root 或有可用 dump 时；或从原厂 fastboot 包提取）：

```bash
# 在设备上（示例，按实际分区名调整）
dd if=/dev/block/by-name/boot_a of=/sdcard/boot_a.img
dd if=/dev/block/by-name/dtbo_a of=/sdcard/dtbo_a.img
# 之后在宿主机压缩提交：
zip stock/boot_a.zip boot_a.img
zip stock/dtbo_a.zip dtbo_a.img
```

恢复原厂（先解压）：

```powershell
unzip stock\boot_a.zip -d stock
unzip stock\dtbo_a.zip -d stock
fastboot flash boot stock\boot_a.img
fastboot flash dtbo stock\dtbo_a.img
fastboot reboot
```

没有 `boot_a.img`（且无 zip）时，仍可构建 rootfs 和 initramfs，只是
`make bootimg` 会失败并提示。
