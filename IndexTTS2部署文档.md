# 一、模型介绍

### IndexTTS2：情感表达和持续时间控制的自动回归零样本文本转语音的突破![image.png](https://foruda.gitee.com/images/1757659790361550570/95a70acb_16012591.png)

现有的自回归大规模文本转语音（TTS）模型在语音自然性方面具有优势，但其逐个标记的生成机制使得合成语音的持续时间难以精确控制。这在需要严格视听同步的应用（例如视频配音）中成为一个重大限制。

本文介绍了 IndexTTS2，该方法提出了一种新颖、通用、自回归的语音时长控制模型友好方法。

该方法支持两种生成模式：一种明确指定生成的标记数量以精确控制语音持续时间;另一种以自回归的方式自由生成语音，无需指定标记的数量，同时忠实地再现输入提示的韵律特征。

此外，IndexTTS2 实现了情感表达和说话者身份之间的解开，实现了对音色和情感的独立控制。在零样本设置中，模型可以准确地重建目标音色（来自音色提示），同时完美再现指定的情感音调（来自风格提示）。

为了提高高度情感表达中的语音清晰度，我们结合了 GPT 潜在表示，并设计了一种新颖的三阶段训练范式，以提高生成语音的稳定性。此外，为了降低情绪控制的门槛，我们通过微调 Qwen3 设计了一种基于文本描述的软指令机制，有效地引导了具有所需情感取向的语音生成。

# 二、模型部署步骤

部署环境

| ubuntu                       | 22.04              |
| ---------------------------- | ------------------ |
| **cuda**               | **12.4.1**   |
| **NVIDIA Corporation** | **RTX 4090** |

### 1.更新基础的软件包

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

## 2.具体部署流程

在当前用户帐户上启用 Git-LFS 插件

```bash
git lfs install
```

![image.png](https://foruda.gitee.com/images/1757659101418719005/d2ec779b_16012591.png)

下载并进入存储库

```bash
git clone https://github.com/index-tts/index-tts.git && cd index-tts
git lfs pull  # download large repository files
```

![image.png](https://foruda.gitee.com/images/1757659138826478145/253f2bfa_16012591.png)

![image.png](https://foruda.gitee.com/images/1757659176525230749/f6fb651f_16012591.png)

安装uv管理包，下载过慢可使用清华镜像源

```bash
pip install -U uv -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/
```

![image.png](https://foruda.gitee.com/images/1757659244819940869/222ff15c_16012591.png)

## 3.基础环境配置

安装所需要的其他依赖项

```bash
uv sync --all-extras --default-index "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"
```

![image.png](https://foruda.gitee.com/images/1757659301062326210/03b7d0e4_16012591.png)

安装modelscope来下载模型

```bash
pip install modelscope -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/
```

![image.png](https://foruda.gitee.com/images/1757659385563011847/04fe7768_16012591.png)

## 4.下载所需要的模型文件

```bash
modelscope download --model IndexTeam/IndexTTS-2 --local_dir checkpoints
```

![image.png](https://foruda.gitee.com/images/1757659428336748366/ab9b9e54_16012591.png)

除了以上模型外，一些小型模型也会自动 在项目首次运行时下载。如果您的网络环境 对 HuggingFace 的访问速度慢，建议执行以下作 命令：

除了以上模型外，项目初次运行时还会自动下载一些小模型，如果您的网络环境访问 HuggingFace 的速度较慢，推荐执行：

```bash
export HF_ENDPOINT="https://hf-mirror.com"
```

我所使用的webui.py 来自于 https://github.com/xldistance/index-tts-2.0-perfect-webui.git

可以 这样使用

```bash
git clone https://github.com/xldistance/index-tts-2.0-perfect-webui.git
```

```bash
ln -s index-tts-2.0-perfect-webui/webui.py webui.py
```

```bash
ln -s index-tts-2.0-perfect-webui/saved_timbres saved_timbres
```

`uv` 需要从 PyPI 或镜像源下载 `cn2an` 包，如果网络不稳定、速度慢或无法访问源，会导致下载卡住。

我们可以选择国内镜像加速下载

## 5.启动模型

```bash
uv run --index-url https://pypi.tuna.tsinghua.edu.cn/simple webui.py
```

在首次运行时会下载一些模型文件，启动完成后会如下显示

![image.png](https://foruda.gitee.com/images/1757662972593957495/76c9d24f_16012591.png)

之后可以通过开放端口进行访问

![image.png](https://foruda.gitee.com/images/1757898383078193453/3cbc17e6_16012591.png)

![image.png](https://foruda.gitee.com/images/1757898396913607308/8da81592_16012591.png)
