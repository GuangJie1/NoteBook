## conda构建说明

### cpu架构
conda构建探测本机架构，构建包区分架构，aarch64和x86_64分别在各架构服务器构建上传。
- aarch64
- x86_64

### 安装工具

```
apt/yum install -y wget
```

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh -O ~/miniconda.sh && \
bash ~/miniconda.sh -b -p /usr/local/miniconda && \
rm -f ~/miniconda.sh

/usr/local/miniconda/bin/conda init bash

source ~/.basrc
```

```
conda install conda-build
conda install anaconda-client
```

### conda/recipe示例

#### meta.yaml
```
{% set version = '8.0.0' %}
{% set name = 'xxx' %}
{% set prefix = 'https://xxx.com/xxx/xxx' %}

{% if target_platform == 'linux-aarch64' %}
{% set arch='aarch64' %}
{% elif target_platform == 'linux-64' %}
{% set arch = 'x86_64' %}
{% else %}
{% set arch = 'unknown' %}
{% endif %}

package:
  name: {{ name }}
  version: {{ version }}

source:
  url: {{ prefix }}{{ version }}/xxx_{{ version }}_linux-{{ arch }}.tar.gz
  
build:
  number: 0
  
outputs:
  - name: {{ name }}
  
about:
  home: https://xxx.com/xxx/xxx
  license_file: LICENSE
  license: xxx
  license_url: xxx
  summary: 'xxxxxx'
  description: |
    xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
  doc_url: https://xxx.com/document/xxxx
```

**备注:**
about内容是可选的，如果要指定license_file，需要将license文件放到构建目录下。

**参数**
- name，软件包名称，自行替换
- version，软件包版本，自行替换
- arch，自动识别无需替换，构建后的包路径如下：
  - x86_64(linux-64), ${miniconda-path/conda-bld/linux-64/*.conda}**
  - aarch64(linux-aarch64), ${miniconda-path/conda-bld/linux-aarch64/*.conda}**

#### build.sh
自定义构建流程。
```
#!/bin/bash

# Get system architecture
LINUX_ARCH=$(python -c "import platform; print(platform.machine())")

# Get the operating system name
LINUX_OS=$(python -c "import platform; print(platform.system())")

# Check if the operating system is Linux
if [ "$LINUX_OS" != "Linux" ]; then
    echo "Error: The package is only supported on Linux"
    exit 1
fi

# Check if the machine architecture is either x86_64 or aarch64
if [ "$LINUX_ARCH" != "x86_64" ] && [ "$LINUX_ARCH" != "aarch64" ]; then
    echo "Error: The package supports only x86_64 or aarch64 architecture"
    exit 1
fi

echo "OS is Linux and architecture is $LINUX_ARCH. Proceeding with the build..."
  
cp -r $SRC_DIR/* $PREFIX/
```

### post-link.sh
安装的钩子脚本，安装conda包时执行安装脚本。
```
#!/bin/bash

# Get system architecture
LINUX_ARCH=$(python -c "import platform; print(platform.machine())")

# Get the operating system name
LINUX_OS=$(python -c "import platform; print(platform.system())")

# Check if the operating system is Linux
if [ "$LINUX_OS" != "Linux" ]; then
    echo "Error: This script is only supported on Linux"
    exit 1
fi

# Check if the machine architecture is either x86_64 or aarch64
if [ "$LINUX_ARCH" != "x86_64" ] && [ "$LINUX_ARCH" != "aarch64" ]; then
    echo "Error: This script supports only x86_64 or aarch64 architecture"
    exit 1
fi

echo "OS is Linux and architecture is $LINUX_ARCH."

# Download the xxxx installer package
PACKAGE_VERSION=1.0.0
PACKAGE_NAME=xxx_${PACKAGE_VERSION}_linux-${LINUX_ARCH}.run
PACKAGE_PATH=$PREFIX/$PACKAGE_NAME
PACKAGE_PREFIX=https://xxx.com/xxx/xxx
wget "$PACKAGE_PREFIX$PACKAGE_VERSION/$PACKAGE_NAME" -O $PACKAGE_PATH

# Install the xxxx
chmod +x $PACKAGE_PATH
echo "Y" | $PACKAGE_PATH --install
```

**参数：**
- PACKAGE_VERSION， 软件包版本，按需替换
- PACKAGE_NAME， 软件报名，按需替换
- PACKAGE_PATH，构建过程中的资源路径，无需改动
- PACKAGE_PREFIX， 软件包云上存储路径前缀，目前固定无需改动

## conda构建流程

### 构建
进入recipe文件目录构建，构建目录下应包含上述recipe构建相关文件。当前构建为meta.yaml、post-link.sh和build.sh(optional)。
```
conda build --croot /path/to/build . 
```
croot指定软件包安装路径，最终路径示例：/path/to/build/conda-bld/linux-64/*.conda。


### 注册
在[anaconda](https://anaconda.org/bioconda/repo)官方仓库注册账号，准备上传conda包。

### 上传
```
anaconda login --username ${username} --password ${password}
anaconda upload /path/to/package
```

> [!NOTE]
> 
> anaconda总容量是10G
> [anaconda/upload](https://enterprise-docs.anaconda.com/en/5.7.1/data-science-workflows/packages/upload.html)。


### 安装

```
conda install -y -c ${channels} ${package}
```

**参数：**
- channels，anaconda个人仓库名称，账号用户名。
- package， 软件包

**其他操作**
```
# 查看已安装的软件包
conda remove -y ${package}
# 移除已安装的软件包
conda list | grep ${package}
```
