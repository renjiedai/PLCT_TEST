# eulaceura测试报告

## 整体介绍

### 测试目的

本报告为eulaceura系统产品的功能测试报告，目的在考察系统的功能完成程度，对于错误给出错误输出日志。

### 测试方法和测试工具

本次测试为功能性测试，由于eulaceura为oerv的一个较为稳定的发行版，所以我们在测试时使用的测试工具和测试例和openEuler系统相同。测试所用脚本链接如下：[链接](https://github.com/brsf11/mugen-riscv)

- 测试方法：自动化测试
- 测试工具：基于mugen的多线程自动化测试脚本

#### 关于mugen

mugen是openEuler社区开放的测试框架，提供公共配置和方法以便社区开发者进行测试代码的编写和执行。

- RISC-V oE自动化测试脚本
  - openEuler的mugen项目目前用于openEuler x86/AArch64的测试，并不方便直接用于当前openEuler RISC-V的测试
    - 测试项目（测试套）并不能很好地匹配
    - 目前的openEuler RISC-V测试依靠QEMU虚拟机
  - mugen中一个测试套对应一个软件或服务，为了更方便地执行测试，可以再抽象一层
  - 辅助测试脚本匹配测试列表和mugen中的测试套，并反馈缺失的测试和执行可用测试

- 在测试时设置了用例超时说明：

  - 默认超时30min

  - 如果某条用例执行超过30min，在用例对TIMEOUT重新赋值

#### 关于自动化多线程测试脚本

qemu_test.py(暂名)实现了自动化测试环境复原和多线程测试的功能，可以实现以下功能：

- qemu_test.py在运行qemu虚拟机的宿主机上运行
- 可使用-F+配置文件或使用参数指定测试的配置，运行测试必须的参数有工作目录、bios或kernel、drive文件
- 若需指定qemu启动参数中-bios为none，需在配置文件中写明bios项为"none"或用-B none，否则qemu启动参数中-bios会省略
- 不指定mugen安装目录程序默认运行测试前准备mugen环境（包括安装git、clone仓库和mugen的依赖安装及结点配置），若待测镜像已准备好mugen，需用"mugenDir"项或-d指定mugen安装目录
- 不指定测试列表文件程序会在测试前遍历软件源生成测试列表，要测试指定的测试套列表，需用"listFile"项或-l参数指定测试列表文件
- `-x`和"threads"项指定运行的线程数
- `-g`和"generate"项指定是否在测试完成后生成通过测试例的列表（格式与测试套描述文件相同）
- working Directory工作目录为qemu镜像所在目录，测试运行完成后日志文件等也会传回工作目录
- list_file为运行的测试套列表
- 对于有riscv版本的测试套，测试套列表中可用原不带riscv后缀的测试套名称，脚本会自动优先匹配有后缀的版本，若想测试原测试套，可在运行qemu_test时加上`-m`参数

### 测试环境

- 测试对象：eulaceura22H1，[下载链接](https://mirror.iscas.ac.cn/openeuler-sig-riscv/eulaceura/image/22H1-RC/)

- 测试执行时用户权限：root
- 系统配置：
- CPU核数：4
  内存：4G
  硬盘容量：10.7G-15G

### 测试套信息

#### 测试用例筛选原则

- mugen-riscv测试例移植的第一步是对mugen原生的测试用例进行筛选，之后才能开始对有问题的测试用例进行修复
- mugen-riscv测试用例筛选本质上就是将通过运行测试查看结果，将mugen X86/AArch64中可直接用于RISC-V oE测试的测试用例筛选出来
- 筛选的对象包括所有mugen中的测试用例（前提是其测的是目前的RISC-V oE有的功能/软件），系统的基本功能优先

#### 测试用例筛选方法

- 运行测试用例，首先去掉结果failed的测试用例（也需结合日志文件判断，排除例如超时的简单问题）
- 检查日志文件
- 现阶段使用的用户模式网络配置的QEMU虚拟机不支持多节点测试和多硬盘，故目前需要排除用到多个节点和多硬盘的测试例（测试套文件中有"add disk"和"machine num"字段的，或可以结合测试用例代码判断）
- 将一个测试套中可用的测试用例整合，形成一个新的测试套描述文件，命名为原文件名+后缀"-riscv"，之后使用自动化测试脚本运行时可仍可使用原测试套名，脚本会自动有限匹配带有riscv后缀的测试套，若想测试原测试套，可在运行runtest.py时加上`-m`参数

在此筛选规范的指导下，截止测试时，选出了206个测试套。本次测试作为验证性测试，测试了如下206个测试套：

| 测试套/软件        | 测试用例总数 |
| ------------------ | ------------ |
| atune              | 1            |
| os-basic           | 56           |
| freeradius         | 7            |
| java-1.8.0-openjdk | 8            |
| p7zip              | 5            |
| portreserve        | 1            |
| rabbitmq-server    | 2            |
| pcp                | 28           |
| xinetd             | 1            |
| cracklib           | 1            |
| ModemManager       | 1            |
| NetworkManager     | 4            |
| abrt               | 1            |
| accountsservice    | 1            |
| acl                | 8            |
| PackageKit         | 2            |
| ImageMagick        | 8            |
| at                 | 1            |
| alsa-utils         | 1            |
| acpid              | 5            |
| arpwatch           | 1            |
| amanda             | 4            |
| attr               | 1            |
| audit              | 1            |
| authd              | 1            |
| authz              | 1            |
| hsqldb             | 1            |
| bcrypt             | 1            |
| avahi              | 3            |
| bind               | 1            |
| cups               | 2            |
| anaconda           | 8            |
| bolt               | 1            |
| brltty             | 1            |
| dnsmasq            | 1            |
| byacc              | 4            |
| clang              | 3            |
| clevis             | 1            |
| cmake              | 6            |
| cloud-init         | 6            |
| colm               | 1            |
| cpio               | 1            |
| ceph               | 9            |
| cppcheck           | 1            |
| cockpit            | 1            |
| clamav             | 11           |
| cronie             | 2            |
| cups-filters       | 1            |
| dbus               | 2            |
| cyrus-sasl         | 1            |
| dovecot            | 1            |
| cvs                | 2            |
| digest-list-tools  | 1            |
| djvulibre          | 5            |
| dkms               | 1            |
| javapackages-tools | 5            |
| dos2unix           | 1            |
| dnssec-trigger     | 1            |
| firebird           | 1            |
| dracut             | 7            |
| e2fsprogs          | 2            |
| galera             | 1            |
| dnf                | 8            |
| erlang             | 2            |
| fcgi               | 1            |
| firewalld          | 3            |
| geoclue2           | 1            |
| fprintd            | 1            |
| flatpak            | 1            |
| git                | 1            |
| ganglia            | 1            |
| grub2              | 1            |
| geos               | 1            |
| glibc              | 2            |
| gpm                | 1            |
| haproxy            | 1            |
| gssproxy           | 1            |
| help2man           | 1            |
| ostree             | 1            |
| haveged            | 1            |
| nss-pam-ldapd      | 1            |
| htop               | 1            |
| httpd              | 1            |
| iputils            | 1            |
| iperf3             | 1            |
| initscripts        | 2            |
| initial-setup      | 1            |
| libreswan          | 2            |
| ipwatchd           | 1            |
| jemalloc           | 2            |
| jq                 | 1            |
| easymock           | 11           |
| kernel             | 2            |
| lm_sensors         | 1            |
| keyutils           | 1            |
| krb5               | 2            |
| libcanberra        | 3            |
| libcap             | 9            |
| libcgroup          | 1            |
| libmemcached       | 12           |
| libosinfo          | 2            |
| kubernetes         | 3            |
| libstoragemgmt     | 1            |
| mdadm              | 1            |
| libwbxml           | 2            |
| lldpad             | 1            |
| libzip             | 3            |
| lxc                | 1            |
| man-db             | 2            |
| mlocate            | 1            |
| lvm2               | 7            |
| multipath-tools    | 1            |
| mksh               | 1            |
| opencc             | 1            |
| memcached          | 3            |
| mt-st              | 1            |
| oddjob             | 1            |
| munge              | 1            |
| net-tools          | 1            |
| netcf              | 1            |
| nghttp2            | 1            |
| mutt               | 1            |
| nginx              | 1            |
| junit5             | 12           |
| open-isns          | 1            |
| nodejs             | 3            |
| nss_wrapper        | 1            |
| nfs-utils          | 8            |
| ntp                | 4            |
| pcsc-lite          | 2            |
| open-iscsi         | 2            |
| openslp            | 1            |
| resource-agents    | 1            |
| opencryptoki       | 1            |
| openhpi            | 1            |
| openldap           | 1            |
| opensp             | 2            |
| opensm             | 1            |
| openssh            | 4            |
| switcheroo-control | 1            |
| optipng            | 2            |
| openwsman          | 1            |
| openssl            | 21           |
| pam                | 1            |
| pacemaker          | 1            |
| openscap           | 4            |
| osc                | 6            |
| perl-Net-Server    | 1            |
| upower             | 1            |
| pesign             | 1            |
| policycoreutils    | 3            |
| polkit             | 1            |
| powertop           | 1            |
| proftpd            | 2            |
| php                | 8            |
| python-blivet      | 1            |
| python-keyring     | 1            |
| varnish            | 2            |
| python-wheel       | 1            |
| quota              | 1            |
| rdate              | 1            |
| rdma-core          | 1            |
| realmd             | 1            |
| redis              | 3            |
| qt5-qttools        | 9            |
| redis5             | 3            |
| rpcbind            | 2            |
| rpmdevtools        | 2            |
| rsyslog            | 1            |
| redis6             | 3            |
| rpmlint            | 2            |
| rrdtool            | 2            |
| sendmail           | 2            |
| rsync              | 2            |
| rtkit              | 1            |
| sane-backends      | 1            |
| sanlock            | 1            |
| sblim-sfcb         | 1            |
| scsi-target-utils  | 1            |
| ruby               | 8            |
| smartmontools      | 1            |
| spice-vdagent      | 2            |
| sqlite             | 17           |
| sqlite-jdbc        | 1            |
| systemtap          | 2            |
| stunnel            | 1            |
| rubygem-bundler    | 13           |
| sssd               | 2            |
| usbmuxd            | 1            |
| sysprof            | 2            |
| sysstat            | 3            |
| swig               | 10           |
| x265               | 1            |
| tang               | 3            |
| telnet             | 1            |
| tftp               | 2            |
| timedatex          | 1            |
| tcllib             | 3            |
| tigervnc           | 1            |
| umoci              | 1            |
| udisks2            | 1            |
| unbound            | 2            |
| tomcat             | 2            |
| wireshark          | 2            |
| uuid               | 2            |
| util-linux         | 3            |
| tidy               | 16           |
| watchdog           | 3            |
| vsftpd             | 2            |
| zvbi               | 1            |
| xmltoman           | 1            |
| xfsprogs           | 1            |
| zerofree           | 1            |
| ypbind             | 1            |
| ypserv             | 3            |
| xdelta             | 3            |

## 测试结果

### 测试结果总览

本次测试共测试了206个测试套，其中失败的测试套为41个

- logs/为所有日志  
- logs_failed/为未通过测试用例的日志  
- tests_passed/为每个测试套中通过的测试例  

### 测试结果展示

这里给出一些测试结果的案例展示，详细的测试结果在同级的三个文件夹中

#### 测试成功展示

下面案例为accountsservice-riscv测试套的accounts-daemon测试例的结果，可以看到当没有发生错误时，未出现ERROR字符。

```
Thu Nov  3 01:11:19 2022 - INFO  - Start environmental preparation.
Thu Nov  3 01:12:07 2022 - INFO  - pkgs:(accountsservice) is already installed
Thu Nov  3 01:12:11 2022 - INFO  - End of environmental preparation!
Thu Nov  3 01:12:14 2022 - INFO  - Start testing...
     Active: active (running) since Thu 2022-11-03 01:12:17 CST; 9s ago
     Active: inactive (dead) since Thu 2022-11-03 01:12:27 CST; 8s ago
     Active: active (running) since Thu 2022-11-03 01:12:38 CST; 9s ago
find: ‘/etc/systemd/system/graphical.target.wants/accounts-daemon.service’: No such file or directory
Created symlink /etc/systemd/system/graphical.target.wants/accounts-daemon.service → /usr/lib/systemd/system/accounts-daemon.service.
/etc/systemd/system/graphical.target.wants/accounts-daemon.service
Failed to reload accounts-daemon.service: Job type reload is not applicable for unit accounts-daemon.service.
     Active: active (running) since Thu 2022-11-03 01:12:38 CST; 59s ago
Thu Nov  3 01:13:42 2022 - INFO  - Finish test!
Thu Nov  3 01:13:46 2022 - INFO  - succeed to execute the case.
Thu Nov  3 01:13:50 2022 - INFO  - start environment cleanup.
Thu Nov  3 01:13:55 2022 - WARN  - no thing to do.
Thu Nov  3 01:13:59 2022 - INFO  - Finish environment cleanup!
```



#### 测试失败结果展示

下面案例为atune-riscv测试套的atune-engine测试例的结果，可以看到在测试时，出现了错误，并且以ERROR的形式予以展示，开发人员可以通过查看日志，来找到可能的失败原因，并进行相应的修复。

```
Sun Oct 30 21:56:08 2022 - INFO  - Start environmental preparation.
sed：无法读取 /etc/atuned/atuned.cnf：No such file or directory
sed：无法读取 /etc/atuned/atuned.cnf：No such file or directory
Failed to start atuned.service: Unit atuned.service not found.
Sun Oct 30 21:58:57 2022 - INFO  - End of environmental preparation!
Sun Oct 30 21:59:02 2022 - INFO  - Start testing...
Failed to restart atune-engine.service: Unit atune-engine.service not found.
Sun Oct 30 21:59:07 2022 - ERROR - atune-engine.service restart failed
Sun Oct 30 21:59:11 2022 - ERROR - ../common/common_lib.sh line 34
Unit atune-engine.service could not be found.
Sun Oct 30 21:59:26 2022 - ERROR - atune-engine.service restart failed
Sun Oct 30 21:59:30 2022 - ERROR - ../common/common_lib.sh line 37
Failed to stop atune-engine.service: Unit atune-engine.service not loaded.
Sun Oct 30 21:59:35 2022 - ERROR - atune-engine.service stop failed
Sun Oct 30 21:59:40 2022 - ERROR - ../common/common_lib.sh line 39
Unit atune-engine.service could not be found.
Sun Oct 30 21:59:55 2022 - ERROR - atune-engine.service stop failed
Sun Oct 30 22:00:00 2022 - ERROR - ../common/common_lib.sh line 42
Failed to start atune-engine.service: Unit atune-engine.service not found.
Sun Oct 30 22:00:04 2022 - ERROR - atune-engine.service start failed
Sun Oct 30 22:00:10 2022 - ERROR - ../common/common_lib.sh line 44
Unit atune-engine.service could not be found.
Sun Oct 30 22:00:25 2022 - ERROR - atune-engine.service start failed
Sun Oct 30 22:00:30 2022 - ERROR - ../common/common_lib.sh line 47
Failed to get unit file state for atune-engine.service: No such file or directory
Sun Oct 30 22:00:35 2022 - INFO  - Unit is indirect, ignoring.
Failed to start atune-engine.service: Unit atune-engine.service not found.
Sun Oct 30 22:00:40 2022 - ERROR - Job type reload is not applicable for unit atune-engine.service
Sun Oct 30 22:00:45 2022 - ERROR - ../common/common_lib.sh line 91
Unit atune-engine.service could not be found.
Unit atune-engine.service could not be found.
Sun Oct 30 22:00:51 2022 - INFO  - Finish test!
Sun Oct 30 22:00:56 2022 - ERROR - failed to execute the case.
Sun Oct 30 22:01:00 2022 - INFO  - start environment cleanup.
Failed to stop atuned.service: Unit atuned.service not loaded.
sed：无法读取 /etc/atuned/atuned.cnf：No such file or directory
sed：无法读取 /etc/atuned/atuned.cnf：No such file or directory
Traceback (most recent call last):
  File "/root/mugen/mugen-riscv/libs/locallibs/rpm_manage.py", line 173, in <module>
    exitcode = rpm_remove(args.node, args.pkgs, args.tempfile)
  File "/root/mugen/mugen-riscv/libs/locallibs/rpm_manage.py", line 144, in rpm_remove
    with open(tmpfile, "r") as f:
FileNotFoundError: [Errno 2] No such file or directory: 'Last metadata expiration check: 0:01:42 ago on 2022年10月30日 星期日 21时57分04秒.\nNo match for argument: atune-engine\nNo match for argument: atune\nError: Unable to find a match: atune-engine atune'
Sun Oct 30 22:01:24 2022 - INFO  - Finish environment cleanup!
```



#### 通过了的测试例

以下案例为os-basic-riscv测试套的测试例通过情况，包含了路径、机器类型等信息。并在cases栏中展示出了通过的测试案例的名字。

```json
{
    "path": "$OET_PATH/testcases/system-test/system-integration/os-basic",
    "machine type": "kvm",
    "add network interface": 2,
    "add disk": [
        2,
        2,
        2,
        2
    ],
    "machine num": 2,
    "cases": [
        {
            "name": "oe_test_system_monitor_process"
        },
        {
            "name": "oe_test_system_log_security"
        },
        {
            "name": "oe_test_system_log_recorded"
        },
        {
            "name": "oe_test_system_log_process"
        },
        {
            "name": "oe_test_system_log_dmesg"
        },
        {
            "name": "oe_test_system_log_device"
        },
        {
            "name": "oe_test_system_log_basic"
        },
        {
            "name": "oe_test_server_vsftpd_restart"
        },
        {
            "name": "oe_test_server_openssh_restart"
        },
        {
            "name": "oe_test_power_powertop_startup"
        },
        {
            "name": "oe_test_power_powertop_calibrate"
        },
        {
            "name": "oe_test_power_output_HTML"
        },
        {
            "name": "oe_test_password_change"
        },
        {
            "name": "oe_test_ProcMgmt_vmstat"
        },
        {
            "name": "oe_test_ProcMgmt_top"
        },
        {
            "name": "oe_test_ProcMgmt_start_kill"
        },
        {
            "name": "oe_test_ProcMgmt_ps"
        },
        {
            "name": "oe_test_ProcMgmt_pgrep"
        },
        {
            "name": "oe_test_ProcMgmt_crontab_cronfile"
        },
        {
            "name": "oe_test_ProcMgmt_crontab_cmd"
        },
        {
            "name": "oe_test_ProcMgmt_at"
        },
        {
            "name": "oe_test_chrony_chronyc_cmd"
        },
        {
            "name": "oe_test_chrony_chronyc_hardwaretime"
        },
        {
            "name": "oe_test_chrony_chronyc_ntpstat"
        },
        {
            "name": "oe_test_chrony_Manuall"
        },
        {
            "name": "oe_test_hwclock"
        },
        {
            "name": "oe_test_group_access"
        },
        {
            "name": "oe_test_kernel_sysctl"
        },
        {
            "name": "oe_test_IOaccess_100Mfile"
        },
        {
            "name": "oe_test_home_directory"
        },
        {
            "name": "oe_test_localectl"
        },
        {
            "name": "oe_test_man"
        },
        {
            "name": "oe_test_basic_UserMgmt_permission"
        },
        {
            "name": "oe_test_basic_set_permissions"
        }
    ]
}
```

### 对于失败case的结果分析

- 结果上相较于openEuler，未通过的测试用例比较多，分析原因可能是由于mugen本身对于eulaceura系统的适配性本身存在一定问题
- 由于运行时设置了30min的超时限制，加之测试时系统选择的性能有限，可能存在一些测试例需要时间较长而导致的失败
- 可能存在源异常问题导致的测试失败
- 虽然存在上述问题，未通过的测试例中仍然可以作为可能存在问题的点，供开发人员修正