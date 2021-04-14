---
lab:
    title: '使用 Bot Framework SDK 创建机器人'
    module: '模块 7 - 对话式 AI 和 Azure 机器人服务'
---

# 使用 Bot Framework SDK 创建机器人

*机器人*是一个软件代理，可参与人类用户的对话式对话。Microsoft Bot Framework 为构建可通过 Azure 机器人服务作为云服务交付的机器人提供了一个综合平台。

在本练习中，你将使用 Microsoft Bot Framework SDK 创建和部署机器人。

## 克隆本课程的存储库

如果尚未将 **AI-102-AIEngineer** 代码存储库克隆到你要在此实验室中使用的环境，请按照以下步骤克隆它。否则，请在 Visual Studio Code 中打开克隆的文件夹。

1. 启动 Visual Studio Code。
2. 打开面板 (Shift+Ctrl+P) 并运行 **Git: Clone** 命令，将 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 存储库克隆到本地文件夹（具体克隆到哪个文件夹无关紧要）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。
4. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **备注**：如果系统提示你添加生成和调试所需的资产，请选择“**以后再说**”。

## 创建机器人

你可以使用 Bot Framework SDK 基于模板创建机器人，然后自定义代码以满足你的特定要求。

> **备注**：在此练习中，可以选择在 **C#** 或 **Python** 中使用 REST API。在下面的步骤中，请执行适用于你的语言首选项的操作。

1. 在 Visual Studio Code 的“**资源管理器**”窗格中，浏览到 **13-bot-framework** 文件夹，并根据你的语言首选项展开 **C-Sharp** 文件夹或 **Python** 文件夹。
2. 右键单击所选语言的文件夹，并打开集成终端。
3. 在终端中，运行以下命令以安装所需的机器人模板和包：

**C#**

```
dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
dotnet new -i Microsoft.Bot.Framework.CSharp.CoreBot
dotnet new -i Microsoft.Bot.Framework.CSharp.EmptyBot
```

**Python**

```
pip install botbuilder-core
pip install asyncio
pip install aiohttp
pip install cookiecutter==1.7.0
```

4. 安装模板和包后，运行以下命令以基于 *EchoBot* 模板创建机器人：

**C#**

```
dotnet new echobot -n TimeBot
```

**Python**

```
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

如果使用的是 Python，则在 cookiecutter 提示时，输入以下详细信息：
- **bot_name**： TimeBot
- **bot_description**：时间机器人
    
5. 在终端窗格中，输入以下命令以将当前目录更改为 **TimeBot** 文件夹，其中列出了已为机器人生成的代码文件：

    ```
    cd TimeBot
    dir
    ```

## 在 Bot Framework Emulator 中测试机器人

你已经基于 *EchoBot* 模板创建了一个机器人。现在，可以在本地运行该机器人，并使用 Bot Framework Emulator（应将其安装在系统上）对其进行测试。

1. 在终端窗格中，确保当前目录是包含机器人代码文件的 **TimeBot** 文件夹，然后输入以下命令以启动在本地运行的机器人。

**C#**

```
dotnet run
```

**Python**

```
python app.py
```
    
机器人启动时，请注意将显示正在运行该机器人的终结点。该终结点类似于 **http://localhost:3978**。

2. 启动 Bot Framework Emulator，然后通过指定附加了 **/api/messages** 路径的终结点来打开机器人，如下所示：

    `http://localhost:3978/api/messages`

3. 在“**实时聊天**”窗格中打开对话后，等待出现消息“*欢迎使用！*”。
4. 输入“*你好*”等消息，并查看机器人的响应，该响应应回显你输入的消息。
5. 关闭 Bot Framework Emulator，并返回到 Visual Studio Code，然后在终端窗口中，按 **Ctrl+C** 停止机器人。

## 修改机器人代码

你已经创建了一个机器人，该机器人将用户的输入回显给他们。这并不是特别有用，但可以用来说明对话式对话的基本流程。与机器人的对话包括一系列*活动*，其中文本、图形或用户界面*卡*用于交换信息。机器人以问候语开始对话，这是用户初始化与机器人的聊天会话时触发的对话*更新活动*的结果。然后，对话包含一系列进一步的活动，在对话中，用户和机器人进行多回合对话以发送*消息*。

1. 在 Visual Studio Code 中，为机器人打开以下代码文件：
    - **C#**： TimeBot/Bots/EchoBot.cs
    - **Python**： TimeBot/bot.py

    请注意，此文件中的代码包含*活动处理程序*函数，一个用于“*已添加成员*”对话更新活动（当某人加入聊天会话时），另一个用于“*消息*”活动（当收到消息时）。对话基于“*回合*”的概念，其中每个回合代表机器人在对话中接收、处理和响应活动的交互。“*回合上下文*”用于跟踪有关当前回合中正在处理的活动的信息。

2. 在代码文件的顶部，添加以下命名空间导入语句：

**C#**

```C#
using System;
```

**Python**

```Python
from datetime import datetime
```

3. 修改*消息*活动的活动处理程序函数以匹配以下代码：

**C#**

```C#
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    string inputMessage = turnContext.Activity.Text;
    string responseMessage = "Ask me what the time is.";
    if (inputMessage.ToLower().StartsWith("what") && inputMessage.ToLower().Contains("time"))
    {
        var now = DateTime.Now;
        responseMessage = "The time is " + now.Hour.ToString() + ":" + now.Minute.ToString("D2");
    }
    await turnContext.SendActivityAsync(MessageFactory.Text(responseMessage, responseMessage), cancellationToken);
}
```

**Python**

```Python
async def on_message_activity(self, turn_context: TurnContext):
    input_message = turn_context.activity.text
    response_message = 'Ask me what the time is.'
    if (input_message.lower().startswith('what') and 'time' in input_message.lower()):
        now = datetime.now()
        response_message = 'The time is {}:{:02d}.'.format(now.hour,now.minute)
    await turn_context.send_activity(response_message)
```
    
4. 保存所做的更改，然后在终端窗格中，确保当前目录是包含机器人代码文件的 **TimeBot** 文件夹，然后输入以下命令以启动在本地运行的机器人。

**C#**

```
dotnet run
```

**Python**

```
python app.py
```

同样，当机器人启动时，请注意将显示正在运行该机器人的终结点。

5. 启动 Bot Framework Emulator，然后通过指定附加了 **/api/messages** 路径的终结点来打开机器人，如下所示：

    `http://localhost:3978/api/messages`

6. 在“**实时聊天**”窗格中打开对话后，等待出现消息“*欢迎使用！*”。
7. 输入“*你好*”等消息，并查看机器人的响应，该响应应为“*问我几点了*”。
8. 输入“*几点了？*”并查看响应。

    机器人现在通过显示机器人运行的本地时间来响应查询“几点了？”。对于任何其他查询，机器人会提示用户询问它几点了。这是一个非常有限的机器人，可以通过与语言理解服务和其他自定义代码集成来进行改进，但是它可以作为一个工作示例，说明如何通过扩展从模板创建的机器人来使用 Bot Framework SDK 构建解决方案。

9. 关闭 Bot Framework Emulator，并返回到 Visual Studio Code，然后在终端窗口中，按 **Ctrl+C** 停止机器人。

## 如果时间允许：将机器人部署到 Azure

现在，你已准备好将应用发布到 Azure。部署涉及多个步骤，例如准备用于部署的代码并创建必要的 Azure 资源。

### 创建或选择资源组

机器人依赖于多个 Azure 资源，这些资源可以在单个资源组中创建。

1. 打开 Azure 门户 (`https://portal.azure.com`)，使用与你的 Azure 订阅关联的 Microsoft 帐户登录。
2. 查看“**资源组**”页面以查看订阅中存在的资源组。
3. 使用“**&#65291;添加**”按钮创建一个新的资源组，该资源组在任何可用区域中都具有唯一的名称。（如果使用的是将你限制为现有资源组的“沙盒”订阅，请记下该资源组的名称）。

### 创建 Azure 应用程序注册

机器人需要应用程序注册，以便能够与用户和 Web 服务进行通信。

1. 在 **TimeBot** 文件夹的终端窗口中，输入以下命令以使用 Azure 命令行接口 (CLI) 登录到 Azure。当浏览器打开时，登录到 Azure 订阅。

```
az login
```

2. 如果有多个 Azure 订阅，请输入以下命令以选择要在其中部署机器人的订阅。

```
az account set --subscription "<YOUR_SUBSCRIPTION_ID>"
```

3. 输入以下命令以使用密码 **Super$ecretPassw0rd** 为 **TimeBot** 创建应用程序注册（如果需要，可以使用其他显示名称和密码，但请记下它们 - 稍后你将用到它们）。

```
az ad app create --display-name "TimeBot" --password "Super$ecretPassw0rd" --available-to-other-tenants
```

4. 命令完成后，将显示大的 JSON 响应。在此响应中，找到 **appId** 值并记下该值。你将在下一个过程用到该值。

### 创建 Azure 资源

当你使用 Bot Framework SDK 通过模板创建机器人时，会为你提供创建必要 Azure 资源所需的 Azure 资源管理器模板。

1. 在 **TimeBot** 文件夹的终端窗格中，输入以下命令（在单个行中），并替换 PLACEHOLDER 值，如下所示：
    - **YOUR_RESOURCE_GROUP**：现有资源组的名称。
    - **YOUR_APP_ID**：在上一过程中记录的 **appId** 值。
    - **区域**：一个 Azure 区域代码（例如 *eastus*）。
    - **所有其他占位符**：将用于命名新资源的唯一值。指定的资源 ID 必须是介于 4-42 个字符之间的全局唯一字符串。记下用于 **BotId** 和 **newWebAppName** 参数的值，稍后你将用到这些值。

```
az deployment group create --resource-group "YOUR_RESOURCE_GROUP" --template-file "deploymenttemplates/template-with-preexisting-rg.json" --parameters appId="YOUR_APP_ID" appSecret="Super$ecretPassw0rd" botId="A_UNIQUE_BOT_ID" newWebAppName="A_UNIQUE_WEB_APP_NAME" newAppServicePlanName="A_UNIQUE_PLAN_NAME" appServicePlanLocation="REGION" --name "A_UNIQUE_SERVICE_NAME"
```

2. 请等待命令执行完毕。如果成功，将显示 JSON 响应。

    如果发生错误，则可能是由于命令中的拼写错误或与现有资源的唯一命名冲突引起的。纠正问题，然后重试。建议使用 Azure 门户删除失败之前创建的任何资源。

3. 命令完成后，在 Azure 门户中查看资源组以查看已创建的资源。

### 准备用于部署的机器人代码

现在，你已经拥有所需的 Azure 资源，可以准备用于部署到这些资源的代码了。

1. 在 Visual Studio Code 中的 **TimeBot** 文件夹的终端窗格中，输入以下命令以准备用于部署的代码依赖项。

**C#**

```
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "TimeBot.csproj"
```

**Python**

```
rmdir /S /Q  __pycache__
notepad requirements.txt
```

- 第二条命令将在记事本中打开 Python 环境的 requirements.txt 文件，对该文件进行修改以匹配以下命令，保存所做的更改，然后关闭记事本。

```
botbuilder-core==4.11.0
aiohttp
```

### 创建用于部署的 zip 存档

要部署机器人文件，需要将其打包为 .zip 存档。该存档必须从机器人的根文件夹中的文件和文件夹创建（<u>请勿</u>压缩根文件夹本身，而是压缩文件夹中的内容！）。

1. 在 Visual Studio Code 的“**资源管理器**”窗格中，右键单击 **TimeBot** 文件夹中的任何文件或文件夹，然后选择“**在文件资源管理器中显示**”。
2. 在“文件资源管理器”窗口中，选择 **TimeBot** 文件夹中的<u>所有</u>文件。然后，右键单击任何选定的文件，然后选择“**发送到**” > “**压缩(zipped)文件夹**”。
3. 将 **TimeBot** 文件夹中生成的压缩文件重命名为 **TimeBot.zip**。

### 部署并测试机器人

现在代码已经准备好，你可以部署它了。

1. 在 Visual Studio Code 中的 **TimeBot** 文件夹的终端窗格中，输入以下命令（在单个行中）以部署打包的代码文件，并替换 PLACEHOLDER 值，如下所示：
    - **YOUR_RESOURCE_GROUP**：现有资源组的名称。
    - **YOUR_WEB_APP_NAME**：创建 Azure 资源时为 **newWebAppName** 参数指定的唯一名称。

```
az webapp deployment source config-zip --resource-group "YOUR_RESOURCE_GROUP" --name "YOUR_WEB_APP_NAME" --src "TimeBot.zip"
```

2. 在 Azure 门户的包含资源的资源组中，打开“**机器人通道注册**”资源（该资源将具有在创建 Azure 资源时分配给 **BotId** 参数的名称）。
3. 在“**机器人管理**”部分，选择“**在 Web 聊天中测试**”。然后等待机器人完成初始化。
4. 输入“*你好*”等消息，并查看机器人的响应，该响应应为“*问我几点了*”。
5. 输入“*几点了？*”并查看响应。

## 使用网页中的 Web 聊天通道

Azure 机器人服务的主要优势之一是能够通过多种*通道*交付机器人。

1. 在 Azure 门户中先前测试机器人的页面上，选择“**通道**”。
2. 请注意，已经自动添加了“**Web 聊天**”通道，并且公共通信平台的其他通道可供使用。
3. 单击“**Web 聊天**”通道旁的“**编辑**”。这将打开一个页面，其中包含将机器人嵌入网页所需的设置。要嵌入机器人，需要提供 HTML 嵌入代码以及为机器人生成的密钥之一。
4. 复制“**嵌入代码**”。
5. 在 Visual Studio Code 中，展开 **13-bot-framework/web-client** 文件夹，然后选择该文件夹中包含的 **default.html** 文件。
6. 在 HTML 代码中，将复制的嵌入代码直接粘贴到注释“**在此处添加机器人的 Iframe**”的下方
7. 返回 Azure 门户，为其中一个密钥（任意密钥均可）选择“**显示**”，并复制该密钥。然后返回到 Visual Studio Code，并将其粘贴到先前添加的 HTML 嵌入代码中，并替换 **YOUR_SECRET_HERE**。
8. 在 Visual Studio Code 的“**资源管理器**”窗格中，右键单击“**default.html**”，并选择“**在文件资源管理器中显示**”。
9. 在“文件资源管理器”窗口中，在 Microsoft Edge 中打开 **default.html**。
10. 在打开的网页中，通过输入“*你好*”来测试机器人。请注意，机器人只有在提交消息后才会初始化，因此问候消息后会立即提示你询问几点了。
11. 通过提交“*几点了？*”来测试机器人。

## 更多信息

要了解有关 Bot Framework 的详细信息，请参阅 [Bot Framework 文档](https://docs.microsoft.com/azure/bot-service/index-bf-sdk)。
