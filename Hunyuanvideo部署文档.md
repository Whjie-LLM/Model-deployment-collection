## 一、模型介绍

镜像拉取时间较长，可以仔细查看模型运行过程，尤其是在使用多卡运行时

我们展示 了 HunyuanVideo，这是一种新颖的开源视频基础模型，它在视频生成方面表现出与领先的闭源模型相当甚至优于领先的闭源模型的性能。HunyuanVideo 具有一个全面的框架，集成了多项关键贡献，包括数据管理、图像-视频联合模型训练以及旨在促进大规模模型训练和推理的高效基础设施。此外，通过有效的模型架构和数据集扩展策略，我们成功训练了一个具有超过 130 亿个参数的视频生成模型，使其成为所有开源模型中最大的。

我们进行了广泛的实验并实施了一系列有针对性的设计，以确保高视觉质量、运动多样性、文本-视频对齐和生成稳定性。根据专业的人体测评结果，混元视频的表现优于之前最先进的模型，包括 Runway Gen-3、Luma 1.6 和 3 款性能最好的中国视频生成模型。通过发布基础模型及其应用的代码和权重，我们旨在弥合闭源和开源视频基础模型之间的差距。这一举措将使社区中的每个人都能够尝试自己的想法，从而培育一个更具活力和活力的视频生成生态系统。

**HunyuanVideo 整体设计**

HunyuanVideo 在时空上进行了训练 压缩的潜在空间，通过因果 3D VAE 进行压缩。文本提示已编码 使用大型语言模型，并作为条件。高斯噪声和条件取为 输入，我们的生成模型生成一个输出潜在，通过 3D VAE 解码器。

![image.png](https://foruda.gitee.com/images/1754633237570112946/3b1151fa_16012591.png)

## 二、模型部署步骤

**基础环境**

| Ubuntu                       | 22.04                 |
| ---------------------------- | --------------------- |
| **cuda**               | **12.4**        |
| **python**             | **3.10.9**      |
| **NVIDIA corporation** | **A100 SXM4**   |
| **torch**              | **2.6.0+cu124** |
| **falsh-attention**    | **2.7.0.post2** |

### 1.更新基础的软件包

查看系统版本信息

```bash
#查看系统的版本信息，包括ID（如ubuntu、centos等）、版本号、名称、版本号ID等
cat /etc/os-release
```

配置国内源

![image.png](https://foruda.gitee.com/images/1752456478684152954/d8a2530a_16012591.png)

apt 配置阿里源

![image.png](https://foruda.gitee.com/images/1752456498725165072/1d793f2d_16012591.png)

将以下内容粘贴进文件中

```bash
deb http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb-src http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
```

### 2.基础Miniconda3环境

查看系统是否有 miniconda 的环境

```bash
conda -V
```

![image.png](https://foruda.gitee.com/images/1752456579966329573/ff822eca_16012591.png)

显示如上输出，即安装了相应环境，若没有 miniconda 的环境，通过以下方法进行安装

```bash
#下载 Miniconda 安装脚本
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
#运行 Miniconda 安装脚本
bash Miniconda3-latest-Linux-x86_64.sh
#初次安装需要激活 base 环境
source ~/.bashrc
```

按下回车键（enter）

![image.png](https://foruda.gitee.com/images/1752456655518696617/a3fdd5ab_16012591.png)

输入 yes

![image.png](https://foruda.gitee.com/images/1752456666692474231/fa937cee_16012591.png)

输入 yes

![image.png](https://foruda.gitee.com/images/1752456683559492251/f18cee00_16012591.png)

安装成功如下图所示

![image.png](https://foruda.gitee.com/images/1752456699919090876/eae94fbf_16012591.png)

### 3.克隆仓库并进入项目

```bash
git clone https://github.com/Tencent-Hunyuan/HunyuanVideo
cd HunyuanVideo
```

![屏幕截图](https://foruda.gitee.com/images/1754635084643119730/215bf5dd_16012591.png)

![屏幕截图](https://foruda.gitee.com/images/1754635247606356074/bfff2e2a_16012591.png)

### 4.创建虚拟环境

创建并激活虚拟环境

```bash
# 1. Create conda environment
conda create -n HunyuanVideo python==3.10.9

# 2. Activate the environment
conda activate HunyuanVideo
```

![屏幕截图](https://foruda.gitee.com/images/1754635129257343503/353f0072_16012591.png)

![屏幕截图](https://foruda.gitee.com/images/1754634523319325946/863870f0_16012591.png)

### 5.安装pytorch环境 (实际版本是2.6.0+cu124 +适配的torchvision，torchaudio)

```bash
conda install pytorch==2.4.0 torchvision==0.19.0 torchaudio==2.4.0 pytorch-cuda=12.4 -c pytorch -c nvidia
```

![屏幕截图](https://foruda.gitee.com/images/1754635442603567472/91f76e2a_16012591.png)

后续出现版本问题，则下载如下版本

### 6.下载依赖

```bash
python -m pip install -r requirements.txt
```

![image.png](https://foruda.gitee.com/images/1754637198213725827/2d76ed79_16012591.png)

### 7.装  **Flash Attention v2** （官方版本 2.6.3）（实际版本2.7.0.post2还是2.7.1.post1记不得了）

```bash
python -m pip install ninja
```

![屏幕截图](https://foruda.gitee.com/images/1754638761879160924/50b972d0_16012591.png)

出现successfully installed 为安装成功

```bash
python -m pip install git+https://github.com/Dao-AILab/flash-attention.git@v2.6.3
```

![屏幕截图](https://foruda.gitee.com/images/1754638697485221271/98692910_16012591.png)

#### 实际版本

若无法下载则直接从flash-attention仓库进行下载

```bash
wget https://github.com/Dao-AILab/flash-attention/releases/download/v2.7.0.post2/flash_attn-2.7.0.post2+cu12torch2.6cxx11abiFALSE-cp310-cp310-linux_x86_64.whl
```

```bash
pip install flash_attn-2.7.0.post2+cu12torch2.6cxx11abiFALSE-cp310-cp310-linux_x86_64.whl
```

### 8.安装xDiT以进行并行推理（官方建议使用torch 2.4.0和flash-attn 2.6.3）实际版本在上面，下载不了就克隆xDiT仓库，从源码下载

```
python -m pip install xfuser==0.4.0
```

### 9.下载HunyuanVideo模型

要下载 HunyuanVideo 模型，请先安装 huggingface-cli。

```basjh
python -m pip install "huggingface_hub[cli]"
```

![image.png](https://foruda.gitee.com/images/1754635694998262315/034efbbc_16012591.png)

然后使用以下命令下载模型：

```bash
# Switch to the directory named 'HunyuanVideo'
cd HunyuanVideo
# Use the huggingface-cli tool to download HunyuanVideo model in HunyuanVideo/ckpts dir.
# The download time may vary from 10 minutes to 1 hour depending on network conditions.
hf download tencent/HunyuanVideo --local-dir ./ckpts
```

![image.png](https://foruda.gitee.com/images/1754635750689677017/eac7819b_16012591.png)

出现下载拒绝是因为没有加载密钥

```
vim download.py
```

```bash
from huggingface_hub import snapshot_download, login
import os

def download_hunyuan_video(local_dir="./ckpts", token="替换为实际密钥"):
    try:
        # 使用提供的密钥登录
        login(token=token)
  
        # 下载模型仓库到指定目录
        print(f"开始下载 tencent/HunyuanVideo 到 {local_dir}...")
        snapshot_download(
            repo_id="tencent/HunyuanVideo",
            local_dir=local_dir,
            local_dir_use_symlinks=False,  # 避免使用符号链接，直接下载文件
            max_workers=4  # 并发下载线程数
        )
        print("下载完成！")
  
    except Exception as e:
        print(f"下载过程中出现错误: {str(e)}")
    finally:
        # 退出登录（可选）
        login(token=None)

if __name__ == "__main__":
    # 调用下载函数
    download_hunyuan_video()

```

下载完成
![image.png](https://foruda.gitee.com/images/1754644620727796520/8832891b_16012591.png)

### 10.下载文本编码器

混元视频使用 MLLM 模型和 CLIP 模型作为文本编码器。

1.MLLM模型（text_encoder文件夹）

混元视频支持不同的 MLLM（包括混元 MLLM 和开源 MLLM 模型）。现阶段，我们还没有发布混元 MLLM。我们建议社区用户使用 [Xtuer](https://huggingface.co/xtuner) 提供的 [llava-llama-3-8b](https://huggingface.co/xtuner/llava-llama-3-8b-v1_1-transformers)，可以通过以下命令下载

```bash
cd HunyuanVideo/ckpts
huggingface-cli download xtuner/llava-llama-3-8b-v1_1-transformers --local-dir ./llava-llama-3-8b-v1_1-transformers
```

![image.png](https://foruda.gitee.com/images/1754880689535665556/6605cbc0_16012591.png)

下载完成会显示匹配所有文件100%

以及出现文件下载目录

为了节省模型加载的 GPU 内存资源，我们将语言模型的各个部分分离成 [llava-llama-3-8b](https://huggingface.co/xtuner/llava-llama-3-8b-v1_1-transformers)和text_encoder

```bash
cd HunyuanVideo
python hyvideo/utils/preprocess_text_encoder_tokenizer_utils.py --input_dir ckpts/llava-llama-3-8b-v1_1-transformers --output_dir ckpts/text_encoder
```

![image.png](https://foruda.gitee.com/images/1754880907974330922/ae759c9a_16012591.png)

加载完成会显示Loading checkpoint shards: 100%

如果显示 no nms 则是torch 与torchvision版本不匹配

首先检查 PyTorch 和 torchvision 的安装情况：

```bash
pip list | grep torch
pip list | grep torchvision
```

![image.png](https://foruda.gitee.com/images/1754881518223678593/28a7b936_16012591.png)

卸载当前版本

```bash
# 若用 conda
conda uninstall -y torchvision

# 若用 pip
pip uninstall -y torchvision
```

![image.png](https://foruda.gitee.com/images/1754881618622288509/73fbc112_16012591.png)

安装0.21.0版本

```bash

# conda 安装（需匹配 CUDA 版本，以 12.4 为例）
conda install torchvision==0.21.0 -c pytorch -c nvidia

# 或 pip 安装（更稳定，避免 conda 源问题）
pip install torchvision==0.21.0 --index-url https://download.pytorch.org/whl/cu124
```

![image.png](https://foruda.gitee.com/images/1754881685986479290/00b3ceb9_16012591.png)

2.CLIP模型（text_encoder_2文件夹）

我们使用 [OpenAI](https://openai.com/) 提供的 [CLIP](https://huggingface.co/openai/clip-vit-large-patch14) 作为另一个文本编码器，社区中的用户可以通过以下命令下载该模型

```bash
cd HunyuanVideo/ckpts
huggingface-cli download openai/clip-vit-large-patch14 --local-dir ./text_encoder_2
```

![屏幕截图](https://foruda.gitee.com/images/1754879797411257912/84b8f5e8_16012591.png)

运行Gradio服务器

```
python3 gradio_server.py --flow-reverse
```

单卡运行可直接查看开放端口，多卡运行接着往下进行

### 11.报错修改

#### 1.缺少函数

```bash
vim /root/miniconda3/envs/HunyuanVideo/lib/python3.10/site-packages/flash_attn/__init__.py
```

将_flash_attn_forward加入到引入的函数中

#### 2.库版本问题

后续若出现 window_size_left相关错误，实际装的是2.7.0.post2应该不会出现window_size_left相关错误

###请将flash-attention更新到2.7.0.post2使用

## 三、模型页面

通过开放端口来直接访问页面

![image.png](https://foruda.gitee.com/images/1754904713890435703/db0a8bcd_16012591.png)

通过选择不同的提示词来生成不同的视频

提示词：A cat walks on the grass, realistic style.步数设置为10步

参数设置
![image.png](https://foruda.gitee.com/images/1754904991807272420/590b4b96_16012591.png)

生成的结果显示，以及耗费时间

花费时间约1:15分钟 ，大小为809KB![image.png](https://foruda.gitee.com/images/1754908486922017445/57084d27_16012591.png)

步数设置为50步时间约为6分钟 ，大小为953.22KB![image.png](https://foruda.gitee.com/images/1754908843570924439/a41740e9_16012591.png)

![image.png](https://foruda.gitee.com/images/1754908815988119892/4c4e7912_16012591.png)

修改参数为最高1280x720 (16:9, 720p)，5s(129f)，50步

时间约为50分钟，大小为2.64MB
![image.png](https://foruda.gitee.com/images/1754912086532450844/c16f9d74_16012591.png)

![image.png](https://foruda.gitee.com/images/1754912223843020549/69b7cf0e_16012591.png)

提示词不要设置太长，会导致错误File name too long错误
