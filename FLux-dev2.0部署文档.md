# 一、介绍

## 主要特点

1. 在开放文本到图像生成、单参考编辑和多参考编辑方面处于领先地位。
2. 无需微调：在一个模型中即可实现角色、对象和风格参考，无需额外训练。
3. 通过指导蒸馏进行训练，使 `FLUX.2 [dev]` 更加高效。
4. 开放权重以推动新的科学研究，并赋予艺术家开发创新工作流程的能力。
5. 生成的输出可用于个人、科学和商业目的，如 [FLUX [dev] 非商业许可协议](https://github.com/black-forest-labs/flux/blob/main/model_licenses/LICENSE-FLUX1-dev)中所述。

## `LUX.2 [dev]`

`FLUX.2 [dev]` 是一个 32B 参数的流量匹配变压器模型，能够生成和编辑（多张）图像。该模型采用 FLUX.2 开发非商业许可证发布，可在此找到。

### 文本转图像示例

[![T2I 网格](https://github.com/black-forest-labs/flux2/raw/main/assets/teaser_generation.png)](https://github.com/black-forest-labs/flux2/blob/main/assets/teaser_generation.png)

### 编辑示例

[![编辑网格](https://github.com/black-forest-labs/flux2/raw/main/assets/teaser_editing.png)](https://github.com/black-forest-labs/flux2/blob/main/assets/teaser_editing.png)

### 提示性上采样

[](https://github.com/black-forest-labs/flux2#prompt-upsampling)

`FLUX.2 [dev]` 及时上采样带来显著益处。下面的推理脚本提供了使用本地提示上采样的选项，使用我们用于文本编码的同一模型（Mistral-Small-3.2-24B-Instruct-2506），或者通过 API 调用使用 OpenRouter 上的任何模型。

请参阅[上采样指南](https://github.com/black-forest-labs/flux2/blob/main/docs/flux2_with_prompt_upsampling.md)，了解更多细节和何时使用上采样的指导。

# 二、本地部署

**注意：本文主要介绍部署到 ComfyUI 中使用**

| 环境    | 版本号 |
| ------- | ------ |
| Python  | =3.12  |
| PyTorch | =2.8.0 |
| cuda    | =12.6  |
| Ubtuntu | 22.0.4 |

## 1.安装 **Miniconda**

**步骤 1：更新系统**
首先，更新您的系统软件包：

```
sudo apt update
sudo apt upgrade -y
```

**步骤 2：下载 Miniconda 安装脚本**
访问 Miniconda 的官方网站或使用以下命令直接下载最新版本的安装脚本（以 Python 3 为例）：

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

**步骤 3：验证安装脚本的完整性（可选）**
下载 SHA256 校验和文件并验证安装包的完整性：

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh.sha256
sha256sum Miniconda3-latest-Linux-x86_64.sh
```

比较输出的校验和与.sha256 文件中的值是否一致，确保文件未被篡改。

**步骤 4：运行安装脚本**
为安装脚本添加执行权限：

```
chmod +x Miniconda3-latest-Linux-x86_64.sh
```

运行安装脚本：

```
./Miniconda3-latest-Linux-x86_64.sh
```

**步骤 5：按照提示完成安装**
安装过程中，您需要：

**阅读许可协议** ：按 Enter 键逐页阅读，或者按 Q 退出阅读。
**接受许可协议** ：输入 yes 并按 Enter。
**选择安装路径** ：默认路径为/home/您的用户名/miniconda3，直接按 Enter 即可，或输入自定义路径。
**是否初始化 Miniconda** ：输入 yes 将 Miniconda 添加到您的 PATH 环境变量中。
**步骤 6：激活 Miniconda 环境**
安装完成后，使环境变量生效：

```
source ~/.bashrc
```

**步骤 7：验证安装是否成功**
检查 conda 版本：

```
conda --version
```

**步骤 8：更新 conda（推荐）**
为了获得最新功能和修复，更新 conda：

```
conda update conda
```

## 2.部署 ComfyUI

### 2.1 克隆代码仓库

```
git clone https://github.com/comfyanonymous/ComfyUI.git
```

### 2.2 安装依赖

* **创建 conda 虚拟环境**

```
conda create -n comfyenv python==3.12
conda activate comfyenv
```

* **安装依赖**

```
cd ComfyUI
pip install -r requirements.txt
```

* **安装** ComfyUI Manager

```
#进入插件的文件
cd /ComfyUI/custom_nodes/
#下载ComfyUI Manager
git clone https://github.com/Comfy-Org/ComfyUI-Manager.git
```

## 3.下载模型

自行前往在huggingface中下载模型
官方地址为：
[Comfy-Org/flux2-dev · Hugging Face](https://huggingface.co/Comfy-Org/flux2-dev)

## 4.启动 ComfyUI

```
python main.py
```

输入网址进入 ComfyUI:

```
http://127.0.0.1:8188
```

![1756369477681_41d0afad_10553073.png](https://suanjiayun.oss-cn-chengdu.aliyuncs.com/communityMirror_pic/1756369477681_41d0afad_10553073.png)
