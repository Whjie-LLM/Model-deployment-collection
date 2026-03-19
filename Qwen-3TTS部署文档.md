### 1.模型简介

![image.png](https://foruda.gitee.com/images/1769502011332068402/f636e9f2_16012591.png)

详细开源信息：[QwenLM/Qwen3-TTS: Qwen3-TTS is an open-source series of TTS models developed by the Qwen team at Alibaba Cloud, supporting stable, expressive, and streaming speech generation, free-form voice design, and vivid voice cloning.](https://github.com/QwenLM/Qwen3-TTS)

Qwen3-TTS 涵盖 10 种主要语言（中文、英语、日语、韩语、德语、法语、俄语、葡萄牙语、西班牙语和意大利语），并支持多种方言语音配置，以满足全球应用需求。此外，模型具备强烈的上下文理解能力，能够根据指令和文本语义自适应控制语调、说话速度和情感表达，并且对噪声输入文本表现出显著提升的鲁棒性。主要特点：

**强大的语音表示** ：由自研的 Qwen3-TTS-Tokenizer-12Hz 驱动，实现高效的声学压缩和高维语义模拟。它完全保留了副语言信息和声学环境特征，使得通过轻量级非 DiT 架构实现高速、高保真度的语音重建。

**通用端到端架构** ：采用离散多码本 LM 架构，实现全信息端到端语音建模。这完全绕过了传统 LM+DiT 方案中固有的信息瓶颈和级联错误，显著提升了模型的多样性、生成效率和性能上限。

极低 **延迟流媒体生成** ：基于创新的双轨混合流媒体生成架构，单一型号支持流媒体和非流媒体生成。它可以在输入一个字符后立即输出第一个音频包，端到端合成延迟低至 97 毫秒，满足实时交互场景的严格要求。

**智能文本理解与语音控制** ：支持由自然语言指令驱动的语音生成，灵活控制音色、情感和韵律等多维声学属性。通过深度整合文本语义理解，模型能够自适应地调整语气、节奏和情感表达，实现逼真的“你想象的即你所听到的”输出。

### 2.系统环境

| python         | 3.13.2            |
| -------------- | ----------------- |
| **cuda** | **12.4**    |
| **显卡** | **RTX3060** |

### 3.更新基础的软件包

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

### 4.安装 **Miniconda**

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

### 5.创建虚拟环境

![image.png](https://foruda.gitee.com/images/1769478371848400140/72d01491_16012591.png)

### 6.克隆项目，激活虚拟环境并安装依赖

```bash
git clone https://github.com/QwenLM/Qwen3-TTS.git
```

![image.png](https://foruda.gitee.com/images/1770630936562556720/d7037371_16012591.png)

安装依赖

```bash
pip install -U qwen-tts
```

![image.png](https://foruda.gitee.com/images/1769478429138216585/e1fc9951_16012591.png)

依赖安装完成

![image.png](https://foruda.gitee.com/images/1769478639223485111/07655182_16012591.png)

### 7.下载模型

```bash
modelscope download --model Qwen/Qwen3-TTS-12Hz-1.7B-Base --local_dir ./Qwen3-TTS-12Hz-1.7B-Base
```

![image.png](https://foruda.gitee.com/images/1769411920582471016/d0119a97_16012591.png)

```bash
modelscope download --model Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign --local_dir ./Qwen3-TTS-12Hz-1.7B-VoiceDesign
```

![image.png](https://foruda.gitee.com/images/1769412250367997381/15c28bb4_16012591.png)

```bash
modelscope download --model Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice --local_dir ./Qwen3-TTS-12Hz-1.7B-CustomVoice
```

![image.png](https://foruda.gitee.com/images/1769412287410969336/6031fc44_16012591.png)

### 8.启动程序

激活环境，进入项目并执行一下命令后即可使用模型

```bash
qwen-tts-demo ./Qwen3-TTS-12Hz-1.7B-Base --ip 0.0.0.0 --port 8080 --dtype bfloat16
```

![image.png](https://foruda.gitee.com/images/1769501874347147985/a7516815_16012591.png)

### 9.使用模型

![image.png](https://foruda.gitee.com/images/1769501911583152354/480125a1_16012591.png)
