# gitee上issue的回归测试说明

### 测试环境说明

根据[Gitee的issue](https://gitee.com/openeuler/RISC-V/issues)以及小组分配，本次回归测试包含了以下4个测试套：

- clamav
- easymock
- freeradius
- java-1.8.0-openjdk

其中clamav和easymock两个测试套的运行环境为0922版本的qemu，freeradius和java-1.8.0-openjdk两个测试套的运行环境为0926版本的qemu。

测试时使用的mugen脚本版本为[自动化测试仓库](https://github.com/renjiedai/mugen-riscv)9月22日的commit版本，版本号为7a657e6acf8090b862837895694e51af9d11298c

### 测试结果

| 测试套名称         | case通过数目 | case未通过数目 |
| ------------------ | ------------ | -------------- |
| clamav             | 11           | 5              |
| easymock           | 11           | 1              |
| freeradius         | 3            | 15             |
| java-1.8.0-openjdk | 8            | 2              |

#### ERROR部分节选

该部分节选了输出的log中报ERROR的部分，其中freeradius未通过数目较多，不排除是测试用计算机的性能问题。

| clamav测试套失败案例名           | 失败原因节选                                                 |
| -------------------------------- | ------------------------------------------------------------ |
| oe_test_clamav_clamonacc         | ERROR: Clamonacc: fanotify_init failed: Function not implemented |
| oe_test_clamav_clamscan_1        | Sat Nov 12 02:45:38 2022 - ERROR - Timeout : Command 'bash -x |
| oe_test_clamav_clamscan_2        | Sat Nov 12 01:56:33 2022 - ERROR - Timeout : Command 'bash -x oe_test_clamav_clamscan_2.sh' timed out after 1799.9976733180001 seconds |
| oe_test_clamav_clamscan_3        | + LOG_ERROR 'Check clamscan --allmatch test_virus_collection --include=REGEX --include-dir=REGEX --max-htmlnotags 5 --max-scriptnormalize 5 failed.' |
| oe_test_service_clamav-clamonacc | Sat Nov 12 02:03:40 2022 - ERROR - clamav-clamonacc.service restart failed |

| easymock测试套失败案例名 | 失败原因节选                               |
| ------------------------ | ------------------------------------------ |
| oe_test_easymock_spring  | ERROR - oe_test_easymock_spring.sh line 32 |

| java-1.8.0-openjdk测试套失败案例名  | 失败原因节选                                           |
| ----------------------------------- | ------------------------------------------------------ |
| oe_test_openjdk_appletviewer_clhsdb | ERROR - oe_test_openjdk_appletviewer_clhsdb.sh line 42 |
| oe_test_openjdk_jdb_jdeps           | ERROR - oe_test_openjdk_jdb_jdeps.sh line 56           |

#### 详细log的位置

详细的logs在openEuler0922和openEuler0926文件夹内的logs的文件夹中