# 一、模型简介

**VibeVoice-ASR** 是一种统一的语音转文本模型，设计用于一次性处理  **60 分钟长音频** ，生成包含  **Who（说话者）、时间戳（Timestmps）和内容（What）** 的结构化转录，并支持 **自定义热词（Custom Hotwords** ）。

**型号：** [VibeVoice-ASR-7B](https://huggingface.co/microsoft/VibeVoice-ASR)
**演示：** [VibeVoice-ASR-演示](https://aka.ms/vibevoice-asr)
**报告：** [VibeVoice-ASR-Report](https://arxiv.org/pdf/2601.18184)
**微调：** [finetune-guide](https://github.com/microsoft/VibeVoice/blob/main/finetuning-asr/README.md)
**vllm：** [vllm-asr](https://github.com/microsoft/VibeVoice/blob/main/docs/vibevoice-vllm-asr.md)

## 🔥 主要特征

**🕒 60 分钟单次处理** ： 与将音频切片成短片段（常常失去全局上下文）的传统 ASR 模型不同，VibeVoice ASR 可接受 64K 令牌长度内最多 **60 分钟**的连续音频输入。这确保了整个小时内说话者的跟踪和语义一致性。

**👤 定制热词** ： 用户可以提供定制的热词（例如特定姓名、专业术语或背景信息）来指导识别流程，显著提高领域特定内容的准确性。

**📝 Rich 转录（谁、何时、什么）：** 该模型共同执行 ASR、日记化和时间戳，生成结构化输出，显示*谁*说*了什么*以及*何时*说了什么。
模型结构如下：

![image.png](https://foruda.gitee.com/images/1769504743968773485/6ebd6717_16012591.png)

# 二、系统环境

| python         | 3.13.2         |
| -------------- | -------------- |
| **cuda** | **12.4** |

## 1.更新基础的软件包

查看系统版本信息

```bash
#查看系统的版本信息，包括ID（如ubuntu、centos等）、版本号、名称、版本号ID等
cat /etc/os-release
```

![image.png](https://foruda.gitee.com/images/1752456456390568045/3a1fcd0c_16012591.png)

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

## 2.安装 **Miniconda**

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

## 3.创建虚拟环境

```bash
conda create -n VV python=3.13.2 -y
```

![image.png](https://foruda.gitee.com/images/1769504884489412076/aaadd34b_16012591.png)

## 4.克隆项目

```bash
git clone https://github.com/microsoft/VibeVoice.git
```

![image.png](https://foruda.gitee.com/images/1769505014658347789/064b09ab_16012591.png)

## 5.进入项目并安装依赖

```
cd VibeVoice
pip install -e .
```

![image.png](https://foruda.gitee.com/images/1769505257467406408/08239286_16012591.png)

## 6.下载模型

```bash
modelscope download --model microsoft/VibeVoice-ASR
```

![image.png](https://foruda.gitee.com/images/1769505342113592224/e43f3659_16012591.png)

## 7.启动程序

```bash
apt update && apt install ffmpeg -y

python -u demo/vibevoice_asr_gradio_demo.py \
  --model_path /VibeVoice-ASR \
  --share \
  --host 0.0.0.0 \
  --port 8080 \
  --attn_implementation sdpa
```

![image.png](https://foruda.gitee.com/images/1769505515405360033/5f833fa9_16012591.png)

## 8.访问端口即可使用模型

![image.png](https://foruda.gitee.com/images/1769506241000676085/910fe8f5_16012591.png)
