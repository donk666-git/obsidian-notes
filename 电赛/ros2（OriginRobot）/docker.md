#### **创建“完美”的Docker环境**

这个步骤将创建一个包含正确Python版本(3.10)的、干净的Ubuntu 20.04环境。

1. **确保您在 `~/horizon_oe_project` 目录下。**
    
2. **创建新的、正确的 `Dockerfile`**。请将以下**全部内容**一次性复制并粘贴到您的终端中，然后按回车。它会覆盖掉旧的Dockerfile文件。
    
    Bash
    
    ```
    cat <<EOF > Dockerfile
    # 使用标准的、x86架构的Ubuntu 20.04作为基础
    FROM ubuntu:20.04
    
    # 设置环境变量，防止安装过程中断
    ENV DEBIAN_FRONTEND=noninteractive
    
    # 更新并安装基础工具，以及添加PPA所需的工具
    RUN apt-get update && apt-get install -y \
        software-properties-common \
        build-essential \
        sudo \
        git \
        curl \
        && rm -rf /var/lib/apt/lists/*
    
    # 添加deadsnakes PPA，这是一个提供新版本Python的可靠软件源
    RUN add-apt-repository ppa:deadsnakes/ppa
    
    # 现在，从新的PPA中安装Python 3.10
    RUN apt-get update && apt-get install -y \
        python3.10 \
        python3.10-venv \
        python3.10-dev \
        python3-pip
    
    # 将系统默认的 python3 命令指向我们新安装的 python3.10
    RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1
    
    # 设置默认启动命令
    CMD ["/bin/bash"]
    EOF
    ```
    
3. **构建这个“完美”的镜像**。
    
    Bash
    
    ```
    docker build -t my-horizon-env-py310 .
    ```
    

#### **C. 启动容器并一站式完成所有安装**

1. **启动容器**，并把您当前的项目文件夹共享进去。
    
    Bash
    
    ```
    docker run -it --gpus all --name oe_final_workspace -v "$(pwd)":/workspace my-horizon-env-py310 bash
    ```
    
    **注意**：运行此命令后，您的终端会进入到一个以`root@...`开头的**全新环境**中。
    
2. **在容器内，一次性完成所有安装**。请将以下**所有命令**一次性复制并粘贴到**容器的终端**里。
    
    Bash
    
    ```
    # 进入共享目录下的主安装文件夹
    cd /workspace/horizon_x5_open_explorer_v1.2.8-py310_20240926/
    
    # 修正脚本的已知bug
    sed -i '1c\#!/bin/bash' ./resolve_all.sh
    sed -i '1c\#!/bin/bash' ./package/host/resolve.sh
    sed -i 's|sh -e ./package/host/resolve.sh|bash -e ./package/host/resolve.sh|' ./resolve_all.sh
    
    # 执行依赖下载
    echo "========= 开始下载依赖... =========="
    ./resolve_all.sh
    
    # 执行安装
    echo "========= 开始安装工具链... =========="
    cd package/host/
    sudo ./install.sh
    
    echo "========= 安装完成！=========="
    ```
    
3. **安装Python依赖库**。
    
    Bash
    
    ```
    # 进入包含requirements.txt的目录
    cd /workspace/horizon_x5_open_explorer_v1.2.8-py310_20240926/package/host/ai_toolchain/
    
    # 安装依赖
    pip install -r requirements.txt
    
    # 如果pycocotools失败，用conda安装（但在此干净环境中pip通常会成功）
    # 如果pip失败，取消下面这行的注释并运行：
    # conda install -c conda-forge pycocotools -y
    ```
    

---

这个流程完成后，您就有了一个功能齐全、环境正确的工作站（在Docker里），并且它只占用了您主系统的一个文件夹共享空间，C盘的压力会小很多。
这个教程将指导您完成以下所有步骤：

1. 在您当前的WSL环境中，创建一个正确的Docker环境。
    
2. 进入Docker环境。
    
3. 在Docker环境中，安装地平线OpenExplorer工具链。
    
4. 最终准备好进行模型转换。
    

---


### **最终详细操作教程 (请按顺序复制粘贴)**

**前提**：

- 您已经安装好了Docker Desktop，并且它已与您的WSL集成。
    
- 您所有的地平线下载文件都存放在 `~/horizon_oe_project` 目录下。
    

#### **第一步：创建Dockerfile**

1. 打开您的WSL终端。
    
2. 确保您在您的主目录 (`~`) 下。
    
    Bash
    
    ```
    cd ~
    ```
    
3. 进入您的项目目录。
    
    Bash
    
    ```
    cd horizon_oe_project
    ```
    
4. 创建一个名为 `Dockerfile` 的新文件。
    
    Bash
    
    ```
    touch Dockerfile
    ```
    
5. 将以下**全部内容**复制并粘贴到您的终端中，然后按回车。这会一次性将所有内容写入`Dockerfile`。
    
    Bash
    
    ```
    cat <<EOF > Dockerfile
    # 使用标准的、x86架构的Ubuntu 20.04作为基础
    FROM ubuntu:20.04
    
    # 设置环境变量，防止apt在构建过程中进行交互式提问
    ENV DEBIAN_FRONTEND=noninteractive
    
    # 安装一些基础的依赖，防止后续脚本报错
    RUN apt-get update && apt-get install -y \
        build-essential \
        python3.8 \
        sudo \
        git \
        && rm -rf /var/lib/apt/lists/*
    
    # 设置默认启动命令
    CMD ["/bin/bash"]
    EOF
    ```
    

#### **第二步：构建并启动Docker容器**

1. 在**同一个目录** (`~/horizon_oe_project`) 下，运行以下命令来构建一个名为 `my-horizon-env` 的新镜像。这个过程会下载Ubuntu基础镜像，需要一些时间。
    
    Bash
    
    ```
    docker build -t my-horizon-env .
    ```
    
2. 构建成功后，运行以下命令来启动一个名为 `oe_workspace` 的容器。这个命令会把您当前的 `horizon_oe_project` 文件夹共享到容器的 `/workspace` 目录中。
    
    Bash
    
    ```
    docker run -it --gpus all --name oe_workspace -v "$(pwd)":/workspace my-horizon-env bash
    ```
    
    **注意**：运行此命令后，您的终端提示符会变成类似 `root@xxxx:/#` 的样子。这表示您已经**成功进入了Docker容器内部**。接下来的所有命令都在这个新终端里执行。
    

#### **第三步：在Docker容器内安装地平线工具链**

**现在，您已经身处一个全新的、干净的Ubuntu 20.04环境中了。**

1. 首先，进入我们共享进来的工作目录。
    
    Bash
    
    ```
    cd /workspace
    ```
    
2. 进入OpenExplorer的主安装文件夹。
    
    Bash
    
    ```
    cd horizon_x5_open_explorer_v1.2.8-py310_20240926/
    ```
    
3. **修正脚本错误**。我们一次性运行之前总结出的所有修正命令，修复脚本的解释器问题。
    
    Bash
    
    ```
    sed -i '1c\#!/bin/bash' ./resolve_all.sh
    sed -i '1c\#!/bin/bash' ./package/host/resolve.sh
    sed -i 's|sh -e ./package/host/resolve.sh|bash -e ./package/host/resolve.sh|' ./resolve_all.sh
    ```
    
4. **执行依赖下载脚本**。这个过程会下载一些大文件，请耐心等待。
    
    Bash
    
    ```
    ./resolve_all.sh
    ```
    
5. **执行安装脚本**。
    
    Bash
    
    ```
    cd package/host/
    sudo ./install.sh
    ```
    
    这一次，`install.sh` 脚本应该会顺利执行并完成，因为它正运行在它所期望的Ubuntu 20.04环境中。
    

#### **第四步：配置环境并验证**

安装完成后，我们需要最后一步来让工具链的命令在任何地方都能被找到。

1. **找到并激活环境**。
    
    Bash
    
    ```
    # 我们用之前找到的env.sh脚本来激活环境
    source /workspace/horizon_x5_open_explorer_v1.2.8-py310_20240926/samples/ai_benchmark/x5/ptq/script/env.sh
    ```
    
2. **验证安装**。运行以下命令：
    
    Bash
    
    ```
    which hb_mapper
    ```
    
    如果它能成功打印出一个路径，例如 `/opt/hobot/.../hb_mapper`，那么恭喜您，**整个环境已经完全配置成功！**
    

---

现在，您的这个Docker容器已经是一个功能完备的地平线模型转换工作站了。您可以开始进行YOLOv8模型的导出和转换工作。

**提示**：下次想再次进入这个配置好的环境时，您不需要重新运行`docker run`，只需要运行以下命令即可：

Bash

```
# 在您主系统的终端里运行
docker start oe_workspace
docker attach oe_workspace
```