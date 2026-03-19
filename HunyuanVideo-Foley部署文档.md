# 一、模型介绍

**🚀 腾讯混源**开源 **HunyuanVideo-Foley** 端到端视频音效生成大模型！

*专为视频内容创作者设计的专业级 AI 工具，广泛适用于短视频创作、电影制作、广告创意、游戏开发等多种场景。*

## 🎯 **核心亮点**

[](https://github.com/Tencent-Hunyuan/HunyuanVideo-Foley#-core-highlights)

**🎬 多场景视听同步**
支持生成与复杂视频场景同步、语义对齐的高质量音频，增强影视和游戏应用的真实感和沉浸式体验。

**⚖️ 多模态语义平衡**
智能平衡视觉和文本信息分析，全面编排音效元素，避免片面生成，满足个性化配音需求。

**🎵 高保真音频输出**
自主研发的 48kHz 音频 VAE 完美重构音效、音乐、人声，实现专业级音频生成品质。

# 二、模型部署步骤

部署环境

| Ubuntu                       | 22.04              |
| ---------------------------- | ------------------ |
| **cuda**               | **12.04**    |
| **python**             | **3.10**     |
| **NVIDIA Corporation** | **RTX 3090** |

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

### 2.基础 Miniconda3 环境

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

## 3.创建虚拟环境

创建名为Hun的虚拟环境

```bash
conda activate -n Hun python==3.10 
```

![image.png](https://foruda.gitee.com/images/1756364300847716465/873c0680_16012591.png)

![image.png](https://foruda.gitee.com/images/1756364318833922739/fbf8a8a6_16012591.png)

激活虚拟环境

![image.png](https://foruda.gitee.com/images/1756374227266673193/fabe7a33_16012591.png)

## 4.从 github 仓库克隆项目

输入命令克隆并进入项目

```bash
git clone https://github.com/Tencent-Hunyuan/HunyuanVideo-Foley.git
cd HunyuanVideo-Foley
```

![image.png](https://foruda.gitee.com/images/1756364646268978476/2609576c_16012591.png)

## 5.下载模型依赖库

```bash
pip install -r requirement.txt
```

![image.png](https://foruda.gitee.com/images/1756365192501752024/a539c394_16012591.png)

![image.png](https://foruda.gitee.com/images/1756365350541373129/5e5eca45_16012591.png)

出现如图即为下载完毕

## 6.下载模型文件

```bash
modelscope download --model Tencent-Hunyuan/HunyuanVideo-Foley --local_dir .
```

![屏幕截图](https://foruda.gitee.com/images/1756372331662545906/5bf0b180_16012591.png)

## 7.运行文件拉起界面

```bash
python gradio_app.py
```

![image.png](https://foruda.gitee.com/images/1756372720021056188/1728a27b_16012591.png)

![image.png](https://foruda.gitee.com/images/1756372878686201122/dc8552f0_16012591.png)

![image.png](https://foruda.gitee.com/images/1756372933406229046/e1e53697_16012591.png)
