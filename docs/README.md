# 编译环境
## 编译需要安装docker
```bash
    apt install docker.io
    sudo usermod -aG docker ${USER}
    newgrp docker
    sudo systemctl restart docker
```

## 官方源码下载代码
```bash
    apt-get -y install git
    git clone --depth=1 --branch=main https://github.com/armbian/build
```

### 分支说明  




- master 分支：Armbian 项目镜像分支，来源为 https://github.com/armbian/build.git
- hugsun_armbian 分支：Hugsun 项目分支，基于 master 分支衍生  

### 同步方法  
```bash
# 克隆本地仓库
git clone git@192.168.0.188:thirdparty/armbian-ubuntu/build.git
cd build

# 添加 ubuntu 远程仓地址
git remote add armbian https://github.com/armbian/build.git

# 示例：同步hugsun_armbian 至最新
git pull armbian master  # 拉取上游分支更新（替换为目标分支）
git checkout hugsun_armbian # 切换到 hugsun 定制分支
git merge master         # 合并上游更新
```

# 编译说明

## 内核编译
```bash
    cd build
    ./compile.sh BOARD=blueberry-edge BRANCH=legacy kernel-config
```

## 编译桌面armbian
```bash
    cd build
    ./compile.sh
    ./compile.sh build BOARD=blueberry-edge BRANCH=legacy BUILD_DESKTOP=yes BUILD_MINIMAL=no DESKTOP_APPGROUPS_SELECTED='browsers chat desktop_tools multimedia office' DESKTOP_ENVIRONMENT=gnome DESKTOP_ENVIRONMENT_CONFIG_NAME=config_base KERNEL_CONFIGURE=yes RELEASE=jammy 
```

```bash
./compile.sh build BOARD=blueberry-edge BRANCH=vendor BUILD_DESKTOP=yes BUILD_MINIMAL=no DESKTOP_APPGROUPS_SELECTED= DESKTOP_ENVIRONMENT=gnome DESKTOP_ENVIRONMENT_CONFIG_NAME=config_base KERNEL_CONFIGURE=no RELEASE=noble 

./compile.sh build BOARD=mekotronics-r58x-pro BRANCH=vendor BUILD_DESKTOP=yes BUILD_MINIMAL=no DESKTOP_APPGROUPS_SELECTED= DESKTOP_ENVIRONMENT=gnome DESKTOP_ENVIRONMENT_CONFIG_NAME=config_base KERNEL_CONFIGURE=no RELEASE=noble 
```

## 编译服务器armbian
```bash
    ./compile.sh build BOARD=blueberry-edge BRANCH=legacy BUILD_DESKTOP=no BUILD_MINIMAL=no KERNEL_CONFIGURE=no RELEASE=jammy
```


