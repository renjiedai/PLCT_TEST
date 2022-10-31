# mugen在eulaceura系统上的配置使用

### 注意事项

- 本文档主要关于mugen在eulaceura系统上的适配
- 建议首先学习mugen在openEuler系统上应用的教程，参考
  - [自动化测试小组仓库readme教程](https://github.com/brsf11/mugen-riscv)
  - [原有的mugen教程](https://github.com/brsf11/Tarsier-Internship/blob/main/Presentation/RISC-V-oE-Autotest-Dev/Markdown/report.md)
  - [mugen使用方法_以os-basic测试套为例](https://github.com/renjiedai/PLCT_TEST/blob/master/WEEK1_20221017-20221023/%E8%87%AA%E5%8A%A8%E5%8C%96%E6%B5%8B%E8%AF%95/mugen%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95_%E4%BB%A5os-basic%E6%B5%8B%E8%AF%95%E5%A5%97%E4%B8%BA%E4%BE%8B.md)
- 本文档在以上三个学习文档的基础上，进行一些额外配置，以完成mugen在eulaceura系统上的应用

### 配置yum源

- 由于目前的(截止2022-10-27)eulaceura发布版并没有安装yum，所以需要先进行yum的安装与配置
- 虽然eulaceura内置了zypper工具，但是由于mugen的依赖是采用的yum管理的，为了方便最好安装yum

#### 配置zypper工具与源

- `zypper ar https://repo.tarsier-infra.com/eulaceura/dist/22H1/M/`  对应Eulaceura-M
  `zypper ar https://repo.tarsier-infra.com/eulaceura/dist/22H1/L/`  对应Eulaceura-L
- 使用`zypper up`命令升级

#### 安装yum与配置源

- 安装yum： `zypper install yum `
- 仿照openEuler配置repo：
  - 进入`/etc/yum.repos.d/`文件夹： `cd /etc/yum.repos.d/`
  - 创建.repo文件： `vim eulaceura.repo`
  - 在文件内加入如下字段：

```
# just for test
[mainline]
name=mainline
baseurl=https://repo.tarsier-infra.com/eulaceura/dist/22H1/M/
enabled=1
gpgcheck=0
# just for test
[epol]
name=epol
baseurl=https://repo.tarsier-infra.com/eulaceura/dist/22H1/L/
enabled=1
gpgcheck=0
```

#### 进行mugen环境的配置

- 从仓库clone下来代码，并进入代码目录
- 运行`bash dep_install.sh`安装依赖
- 配置测试套环境变量以及运行demo参照如下教程
  - [mugen使用方法_以os-basic测试套为例](https://github.com/renjiedai/PLCT_TEST/blob/master/WEEK1_20221017-20221023/%E8%87%AA%E5%8A%A8%E5%8C%96%E6%B5%8B%E8%AF%95/mugen%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95_%E4%BB%A5os-basic%E6%B5%8B%E8%AF%95%E5%A5%97%E4%B8%BA%E4%BE%8B.md)

可以看到效果和openEuler一致，环境配置完成