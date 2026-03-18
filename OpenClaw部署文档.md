# 一、本地模型部署

## 1.安装ollama

```
curl -fsSL https://ollama.com/install.sh | sh
```

![image.png](https://foruda.gitee.com/images/1769917841584045661/8926bac5_16012591.png)

## 2.启动ollama

![image.png](https://foruda.gitee.com/images/1769917868036300073/7513d4de_16012591.png)

## 3.打开新的终端页面并安装模型

```
ollama run glm-4.7-flash:latest
```

![image.png](https://foruda.gitee.com/images/1769917910620949652/38db9d01_16012591.png)

按ctrl+d退出

确保模型启动

# 二、安装OpenClaw 相关依赖及配置

## 1.安装openclaw并配置

推荐cli安装（先开启代理连接外网）

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

![image.png](https://foruda.gitee.com/images/1769918045399989730/1213ce26_16012591.png)

检测完成后开始进行配置

![image.png](https://foruda.gitee.com/images/1769918113133298147/08322123_16012591.png)

基础配置选择

```bash
I understand this is powerful and inherently risky. Continue?
yes
Onboarding mode
QuickStart
Model/auth provider  #可以选择qwen，是免费的，我们这里是使用ollama的自建模型，所以跳过
Skip for now
Filter models by provider
All providers
Default model
Keep current (default: anthropic/claude-opus-4-5)
Select channel (QuickStart) #按照自己的需求，可以使用telegram以及WhatsApp等，这里暂时跳过
Skip for now
Configure skills now? (recommended) #暂时跳过，按需配置
NO
Enable hooks?
Skip for now
Install shell completion script?
Yes
#基础配置完成
```

配置完成后，初始话环境变量

```bash
source /root/.bashrc
```

后续如果有新要求可以执行下面的命令进行重新配置

```bash
openclaw onboard --install-daemon
```

## 2.修改配置为本地模型

```bash
cat > ~/.openclaw/openclaw.json << 'EOF'
{
  "messages": {
    "ackReactionScope": "group-mentions"
  },
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://127.0.0.1:11434/v1",
        "apiKey": "ollama-local",
        "api": "openai-responses",
        "models": [
          {
            "id": "glm-4.7-flash:latest",
            "name": "GLM 4.7 Flash",
            "reasoning": true,
            "input": ["text"]
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/glm-4.7-flash:latest"
      }
    }
  },
EOF "lastTouchedAt": "2026-02-01T03:40:00.000Z"7f0135668c81bf01e"
```

同时查看模型是否已经通过ollama启动，显存占用，即是启动

![image.png](https://foruda.gitee.com/images/1769926160299539437/dbae6363_16012591.png)

## 3.启动网关进行使用

执行下面的命令，启动 OpenClaw 的核心控制台（Gateway）进程

```bash
openclaw gateway --port 18789
```

出现下面的显示，则是之前启动的进程没有关闭，使用下述的命令关闭后，再重新启动

![image.png](https://foruda.gitee.com/images/1770085183475997740/d4fb4bf6_16012591.png)

```bash
kill -9 604
```

执行成功后访问，网址后缀必须带上配置好的token，不然无法使用

```plaintext
http://127.0.0.1:18789/?token=64bf85fa9bb2afe9e514d865f7c5d4f7f0135668c81bf01e
```

# 三、调整配置实现智能体工具调用

上述方法即可实现与机器人聊天，但是由于前期参数设置过小导致无法控制命令智能体操作服务器，现在进行修改，实现智能体能够操作服务器

## 1.下述命令执行修改配置

将模型名字进行覆盖，确保智能体触发权限能使用工具来操作服务器

```bash
ollama cp glm-4.7-flash:latest qwen-coder-local:latest
```

创建指令文件，强制模型使用工具

```bash
mkdir -p /root/.openclaw/workspace
echo "You are a Linux Root Admin. Use 'native_run' or 'python_interpreter' for all system tasks. NEVER hallucinate output. Output must be concise to avoid WebUI lag." > /root/.openclaw/workspace/instructions.md
```

使用下面的文件来覆盖之前的配置文件/root/.openclaw/openclaw.json

```bash
{
  "meta": {
    "lastTouchedVersion": "2026.1.30",
    "lastTouchedAt": "2026-02-02T14:50:00.000Z"
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/qwen-coder-local:latest"
      },
      "models": {
        "ollama/qwen-coder-local:latest": {
          "alias": "system-admin"
        }
      },
      "workspace": "/root/.openclaw/workspace",
      "maxConcurrent": 2
    }
  },
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://127.0.0.1:11434/v1",
        "apiKey": "ollama-local",
        "api": "openai-responses",
        "models": [
          {
            "id": "qwen-coder-local:latest",
            "name": "Local System Coder",
            "contextWindow": 16384,
            "maxTokens": 4096
          }
        ]
      }
    }
  },
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "6535b8d1c9304bd8a4792d1b7787422c8fdede9fe8924540"
    }
  },
  "commands": {
    "native": "auto",
    "nativeSkills": "auto"
  }
}
```

赋予权限并启动 ，使用以下命令组合启动：

```bash
export OPENCLAW_ALLOW_NATIVE_RUNS=true
export OPENCLAW_ENABLE_INTERPRETER=true
export OLLAMA_NUM_CTX=16384

openclaw gateway --allow-unconfigured
```

出现下面的显示，则是之前启动的进程没有关闭，使用下述的命令关闭后，再重新启动

![image.png](https://foruda.gitee.com/images/1770085183475997740/d4fb4bf6_16012591.png)

```bash
kill -9 604
```

接下来再次打开网址即可实现通过webchat界面命令智能体操作服务器

```
http://127.0.0.1:18789/?token=64bf85fa9bb2afe9e514d865f7c5d4f7f0135668c81bf01e
```

## 2.使用缺陷

由于我们使用的是远程服务器通过远程连接到本地，再通过vscode实现端口转发，就会导致向模型进行提问时，模型的回答内容到前端，以及后台处理出现冲突，导致返回的内容在前端渲染不及时，出现白屏情况，属于正常问题，如果是使用本地服务器部署则不会出现问题

具体白屏出现在两个地方

1.输入问题并点击发送的那一刻，会出现短暂白屏，几秒后显示出模型正在思考的画面

2.第二次出现在模型将回答返回渲染在前端的那一刻，由于大量数据传输过来，导致渲染不及时，出现白屏，渲染完成后就会显示正常

所以我们建议通过外部应用操控智能体

# 四、外部应用操控智能体

接下来讲解如何实现外部应用来连接智能体，并通过自然语言来命令其操作服务器

示例：飞书

## 1.在系统中安装飞书插件

```bash
openclaw plugins install @m1heng-clawd/feishu
```

![image.png](https://foruda.gitee.com/images/1769998243793122998/b337ee5a_16012591.png)

## 2.开始进行机器人配置

接下来，进入飞书开放平台添加机器人，[飞书开放平台](https://open.feishu.cn/)

进行注册登录后点击开发者平台

![image.png](https://foruda.gitee.com/images/1770026008785629355/4a51eb68_16012591.png)

点击创建企业自建应用来创建应用，并输入“应用名称”，“应用描述”，选择应用图标后点击创建

点击添加机器人

![image.png](https://foruda.gitee.com/images/1770026158021773735/63407f66_16012591.png)

点击凭证与基础信息，记录下App ID和App Secret，之后会使用到 ![image.png](https://foruda.gitee.com/images/1770026273771312503/bd267f90_16012591.png)

点击右侧权限管理，点击开通权限

依次输入

```plaintext
contact:user.base:readonly
```

输入并开通全部权限

```php-template
im:message
```

输入并开通权限

```php-template
im:resource
```

## 3.重新回到终端写入机器人信息

再次进入openclaw 的配置终端，输入如下几条命令，将占位字符替换为你的实际App ID和App Secret

```bash
openclaw config set channels.feishu.appId "你的APP ID"
openclaw config set channels.feishu.appSecret "你的APP Secret"
openclaw config set channels.feishu.enabled true
openclaw config set channels.feishu.connectionMode "websocket"
```

接下来重启网关，并保证终端不被关闭

```bash
openclaw gateway --port 18789 --verbose --allow-unconfigured
```

出现下面的显示，则是之前启动的进程没有关闭，使用下述的命令关闭后，再重新启动

![image.png](https://foruda.gitee.com/images/1770085183475997740/d4fb4bf6_16012591.png)

```bash
kill -9 604
```

重新回到飞书开放平台进行配置，先选择事件与回调，订阅方式选择长连接，点击保存，再点击添加事件，输入下面的事件并点击确认添加

```plaintext
im.message.receive_v1
```

![image.png](https://foruda.gitee.com/images/1770083087105145026/c7a09640_16012591.png)

添加完成后，点击左侧的版本管理与发布，点击创建版本输入相关信息后点击确认发布

审核完成后即可在飞书手机端向机器人发送信息来控制电脑进行一系列操作

## 4.输入问题，得到反馈，配置完成

![9bf4567231ddc901b725c5fa5c801a80.jpg](https://foruda.gitee.com/images/1770083687034874900/dd57014e_16012591.jpg)

得到有效输出既是配置成功
