# Anolis操作系统自动化测试报告(Part)

本次测试为Anolis操作系统的自动化测试报告，本部分涵盖了73个测试套。

### 基本信息

- 测试框架：mugen-riscv

- 测试环境

  - CPU核数：8

  - 内存大小：8G

  - qemu启动参数:

    ```
    qemu-system-riscv64 \
    -nographic -machine virt  \
    -smp 8 -m 8G \
    -audiodev pa,id=snd0 \
    -kernel u-boot.bin \
    -bios fw_dynamic.bin \
    -drive file=anolisos-disk-new.qcow2,format=qcow2,id=hd0 \
    -object rng-random,filename=/dev/urandom,id=rng0 \
    -device virtio-rng-device,rng=rng0 \
    -device virtio-blk-device,drive=hd0 \
    -device virtio-net-device,netdev=usernet \
    -netdev user,id=usernet,hostfwd=tcp::"ssh_port"-:22 \
    -device qemu-xhci -usb -device usb-kbd -device usb-tablet -device usb-audio,audiodev=snd0 \
    -append 'root=/dev/vda1 rw console=ttyS0 swiotlb=1 loglevel=3 systemd.default_timeout_start_sec=600 selinux=0 highres=off mem=8192M earlycon' 
    ```

- 测试例位置：[GitHub地址](https://github.com/brsf11/mugen-riscv/tree/riscv/testcases/cli-test)

### 测试结果文件结构

- logs文件夹：所有测试用例的日志文件
- logs_failed文件夹：所有未通过测试用例的日志文件
- suite2cases_out文件夹：对输出结果进行展示，以json格式显示

### 完整版测试报告地址

GitHub地址如下：[仓库链接](https://github.com/brsf11/Tarsier-Internship/tree/main/Testing/RISCVAnolisPkgTestDec18)