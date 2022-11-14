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

[注]：详细的测试结果在对应文件夹的logs文件中