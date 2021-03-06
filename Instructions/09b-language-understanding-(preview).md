---
lab:
    title: '通过语言服务创建语言理解模型（预览）'
---

# 通过语言服务创建语言理解模型（预览）

> **备注**：语言服务的对话语言理解功能当前处于预览阶段，可能会有更改。在一些情况下，模型训练可能会失败 - 如果失败，请重试。  

语言服务使你能够定义对话语言理解模型，应用程序可使用该模型来解释用户的自然语言输入、预测用户的意图（他们想要实现的目标），以及识别应应用该意图的任何实体。

例如，时钟应用程序的语言理解模型可能会处理如下输入：

*What is the time in London?（伦敦现在几点了？）*

这种输入是一种言语（用户可能说出或键入的内容）示例，对于此示例，预期意图是获取特定位置（一个实体）的时间；在本例中，此位置为“*伦敦*”。

> **备注**：语言理解模型的任务是预测用户的意图，并识别应用该意图的任何实体。它<u>不</u>需要实际去执行满足意图所需的操作。例如，时钟应用程序可以使用语言应用来识别用户想要了解伦敦的时间；但必须由客户端应用程序本身来实现此逻辑以确定正确时间并将其呈现给用户。

## 创建语言服务资源

如需创建对话语言理解模型，需要支持的区域中的**语言服务**资源。本文撰写时，仅支持“美国西部 2”和“西欧”区域。

1. 打开 Azure 门户 (`https://portal.azure.com`)，使用与你的 Azure 订阅关联的 Microsoft 帐户登录。
2. 选择“**&#65291;创建资源**”按钮，搜索“*语言*”，并使用以下设置创建一个**语言服务**资源。

    - **订阅**：*你的 Azure 订阅*
    - **资源组**：*选择或创建一个资源组（如果你使用的是受限订阅，则可能无权创建新资源组，在此情况下，可使用一个已提供的资源组）*
    - **区域**：美国西部 2、西欧
    - **名称**：*输入唯一名称*
    - **定价层**：免费 (F0)（*如果该层级不可用，请选择标准(S)*）
    - **法律条款**： _同意_ 
    - **负责任的 AI 通知**： _同意_
3. 等待部署完成，然后查看部署详细信息。

## 创建对话语言理解项目

现在，你已经创建了创作资源，接下来，可以使用它来创建对话语言理解项目。

1. 在新的浏览器选项卡中，打开 Language Studio 门户 (`https://language.azure.com`)，并使用与你的 Azure 订阅关联的 Microsoft 帐户登录。
2. 如果系统提示选择一个语言资源，请选择以下设置：
    - **Azure 目录**：包含订阅的 Azure 目录。
    - **Azure 订阅**：你的 Azure 订阅。
    - **语言资源**：你之前创建的语言资源。
3. 如果<u>未</u>提示选择一个语言资源，可能是因为你已分配其他语言资源；在这种情况下：
    1. 在页面顶部栏上，单击“**设置 (&#9881;)**”按钮。
    2. 在“**设置**”页面上，查看“**资源**”选项卡。
    3. 选择刚创建的语言资源，单击“**切换资源**”。
    4. 在页面顶部，单击“**Language Studio**”，返回 Language Studio 主页。
4. 在门户顶部的“**创建新项**”菜单中，选择“**对话语言理解**”。
5. 在“**创建项目**”对话框中的“**选择项目类型**”页面中，选择“**对话**”，单击“**下一步**”。
6. 在“**输入基本信息**”页面，输入以下详细信息，单击“**下一步**”：
    - **名称**： `Clock`
    - **描述：** `Natural language clock`
    - **言语主要语言**：英语
    - **在项目中启用多种语言？**：*未选定*
7. 在“**查看并完成**”页面上，单击“**创建**”。

## 创建意图

我们在新项目中要做的第一件事就是定义一些意图。

> **提示**：处理项目时，如果显示一些提示，请阅读提示并单击“**了解**”以消除提示，或单击“**全部跳过**”。

1. 在“**生成架构**”页面上的“**意图**”选项卡中，选择“**&#65291; 添加**”，添加名为 **GetTime** 的新意图。
2. 单击新的 **GetTime** 意图以进行编辑，添加以下言语作为示例用户输入：

    `what is the time?`

    `what's the time?`

    `what time is it?`

    `tell me the time`

3. 添加这些言语后，单击“**保存更改**”，返回“**生成架构**”页面。

4. 添加名为 **GetDay** 的另一个新意图以及以下言语：

    `what day is it?`

    `what's the day?`

    `what is the day today?`

    `what day of the week is it?`

5. 添加并保存这些言语后，返回“**生成架构**”页面，并使用以下言语添加另一个名为 **GetDate** 的新意图：

    `what date is it?`

    `what's the date?`

    `what is the date today?`

    `what's today's date?`

6. 添加这些言语后，将其保存并清除“言语”页面上的 **GetDate** 筛选器，以便可以查看所有意图的所有言语。

## 训练和测试模型

现在，你已经添加了一些意图之后，接下来，让我们训练语言模型，查看它是否能够通过用户输入正确预测这些意图。

1. 在左侧窗格中，选择“**训练模型**”页面，选择训练新模型的选项，将其命名为“**Clock**”，并确保选中训练时运行评估的选项。单击“**训练**”以训练模型。
2. 训练完成后（可能会花一些时间），查看“**查看模型详细信息**”页面并选择“**Clock**”模型。然后查看整体和每个意图的评估指标（*精准率*、*召回率*和 *F1 分数*）以及训练时执行的评估生成的融合矩阵（请注意，由于示例言语数量小，所以结果中并不包含所有意图）。

    >**备注**：如需详细了解评估指标，请参阅[文档](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/concepts/evaluation-metrics)

3. 在“**部署模型**”页面上选择 **Clock** 模型并进行部署。这可能需要一些时间。
4. 部署模型后，在“**测试模型**”页面上选择 **Clock** 模型。
5. 输入以下文本，然后单击“**运行测试**”：

    `what's the time now?`

    检查返回的结果，请注意，该结果包含预测意图（应为 **GetTime**）以及一个置信度分数，该分数表示模型计算出预测意图的概率。JSON 选项卡显示每个可能意图的比较置信度（置信度分数最高的意图是预测的意图）

6. 清除文本框，然后使用以下文本运行另一个测试：

    `tell me the time`

    再次查看预测意图和置信度分数。

7. 请尝试以下文本：

    `what's the day today?`

    模型有望预测 **GetDay** 意图。

## 添加实体

到目前为止，你已定义了一些与意图有关的简单言语。大多数实际应用程序都包含更复杂的言语，必须从这些言语中提取特定的数据实体才能获取有关意图的更多上下文。

### 添加学习实体

最常见的一种实体是学习实体，在此实体中，模型根据示例学习识别实体值。

1. 在 Language Studio 中，返回“**生成架构**”页面，然后在“**实体**”选项卡中，选择“**&#65291; 添加**”以添加新实体。
2. 在“**添加实体**”对话框中，输入实体名称“**Location**”，确保已选中“**学习**”。然后单击“**添加实体**”。
3. 创建“**Location**”实体后，返回“**生成架构**”页面，在“**意图**”选项卡中选择“**GetTime**”意图。
4. 输入以下新示例言语：

    `what time is it in London?`

5. 添加该言语后，选择“**London**”一词，然后在出现的下拉列表中，选择“**Location**”以指示“*London*”即为一个位置示例。
6. 添加另一示例言语：

    `Tell me the time in Paris?`

7. 添加该言语后，选择“***Paris***”一词，并将其映射到“**Location**”实体。
8. 添加另一示例言语：

    `what's the time in New York?`

9. 添加该言语后，选择“***New York***”一词，并将其映射到“**Location**”实体。

10. 单击“**保存更改**”保存新言语。

### 添加列表实体

在某些情况下，实体的有效值可被限制为一系列特定术语和同义词；这可帮助应用识别言语中的实体实例。

1. 在 Language Studio 中，返回“**生成架构**”页面，然后在“**实体**”选项卡中，选择“**&#65291; 添加**”以添加新实体。
2. 在“**添加实体**”对话框中，输入实体名称“**Weekday**”，选择“**列表**”实体类型。然后单击“**添加实体**”。
3. 在“**Weekday**”实体的页面的“**列表**”部分中，单击“**&#65291; 添加新列表**”。然后输入以下值和同义词，单击“**保存**”：

    | 值 | 同义词|
    |-------------------|---------|
    | 星期六 | 周六 |

4. 重复上一步，添加以下列表部分：

    | 值 | 同义词|
    |-------------------|---------|
    | Monday | Mon |
    | Tuesday | Tue, Tues |
    | Wednesday | Wed, Weds |
    | Thursday | Thur, Thurs |
    | Friday | Fri |
    | Saturday | Sat |

5. 返回“**生成架构**”页面，在“**意图**”选项卡上选择“**GetDate**”意图。
6. 输入以下新示例言语：

    `what date was it on Saturday?`

7. 添加言语后，选择“***Saturday***”一词，然后在出现的下拉列表中选择“**Weekday**”。
8. 添加另一示例言语：

    `what date will it be on Friday?`

9. 添加该言语后，将“**Friday**”映射到“**Weekday**”实体。

10. 添加另一示例言语：

    `what will the be on Thurs?`

11. 添加该言语后，将“**Thurs**”映射到“**Weekday**”实体。

12. 单击“**保存更改**”保存新言语。

### 添加预构建实体

语言服务提供对话应用程序中常用的一系列预构建实体。

1. 在 Language Studio 中，返回“**生成架构**”页面，然后在“**实体**”选项卡中，选择“**&#65291; 添加**”以添加新实体。
2. 在“**添加实体**”对话框中，输入实体名称“**Date**”，选择“**预构建**”实体类型。然后单击“**添加实体**”。
3. 在“**Date**”实体的页面的“**预构建**”部分中，单击“**&#65291; 添加新的预构建实体**”。
4. 在“**选择预构建实体**”列表中，选择“**DateTime**”，然后单击“**保存**”。
5. 返回“**生成架构**”页面，在“**意图**”选项卡上选择“**GetDay**”意图。
6. 输入以下新示例言语：

    `what day was 01/01/1901?`

7. 添加言语后，选择“***01/01/1901***”，然后在出现的下拉列表中选择“**Date**”。
8. 添加另一示例言语：

    `what day will it be on Dec 31st 2099?`

9. 添加该言语后，将“**Dec 31st 2099**”映射到“**Date**”实体。

10. 单击“**保存更改**”保存新言语。

### 重新训练模型

现在，你已经修改了架构，接下来，需要重新训练并重新测试模式。

1. 在“**训练模型**”页面中，选择覆盖现有模型的选项并指定 **Clock** 模型。确保选中训练时运行评估的选项，单击“**训练**”以训练模型；确认你希望覆盖现有模型。
2. 训练完成后（可能会花一些时间），查看“**查看模型详细信息**”页面并选择“**Clock**”模型。然后查看整体、每个实体和每个意图的评估指标（*精准率*、*召回率*和 *F1 分数*）以及训练时执行的评估生成的融合矩阵（请注意，由于示例言语数量小，所以结果中并不包含所有意图）。
3. 在“**部署模型**”页面上选择 **Clock** 模型并进行部署。这可能需要一些时间。
4. 部署应用后，在“**测试模型**”页面，选择“**Clock**”模型，然后使用以下文本测试它：

    `what's the time in Edinburgh?`

5. 查看返回的结果，我们希望它能预测 **GetTime** 意图和“**Location**”实体（文本值为“*Edinburgh*”）。
6. 尝试测试以下言语：

    `what time is it in Tokyo?`
    
    `what date is it on Friday?`

    `what's the date on Weds?`

    `what day was 01/01/2020?`

    `what day will Mar 7th 2030 be?`

## 使用客户端应用中的模型

在实际项目中，你需要反复优化意图和实体、重新训练并重新测试，直到对预测性能感到满意为止。对其进行测试后，如果你对预测性能感到满意，可以通过调用其 REST 接口将其用于客户端应用。在此练习中，你将使用 *curl* 实用工具调用模型的 REST 终结点。

1. 在 Language Studio 中的“**部署模型**”页面，选择“**Clock**”模型。然后，单击“**获取预测 URL**”。
2. 在“**获取预测 URL**”对话框中，注意到预测终结点的 URL 随示例请求一同显示，该请求包含将 HTTP POST 请求提交到终结点的 **curl** 命令，指定标头中语言资源的键并包含请求数据中的查询和语言。
3. 复制示例请求并将其粘贴到你的首选文本编辑器（例如记事本）。
4. 替换以下占位符：
    - **YOUR_QUERY_HERE**：*What's the time in Sydney*
    - **QUERY_LANGUAGE_HERE**： *EN*

    该命令应类似以下代码：

    ```
    curl -X POST "https://some-name.cognitiveservices.azure.com/language/:analyze-conversations?projectName=Clock&deploymentName=production&api-version=2021-11-01-preview" -H "Ocp-Apim-Subscription-Key: 0ab1c23de4f56..."  -H "Apim-Request-Id: 9zy8x76wv5u43...." -H "Content-Type: application/json" -d "{\"verbose\":true,\"query\":\"What's the time in Sydney?\",\"language\":\"EN\"}"
    ```

5. 打开命令提示符 (Windows) 或 bash shell (Linux/Mac)。
6. 复制已编辑的 curl 命令，将其粘贴到命令行接口并运行。
7. 查看生成的 JSON，其中应包含预测的意图和实体，类似于以下内容：

    ```
    {"query":"What's the time in Sydney?","prediction":{"topIntent":"GetTime","projectKind":"conversation","intents":[{"category":"GetTime","confidenceScore":0.9998859},{"category":"GetDate","confidenceScore":9.8372206E-05},{"category":"GetDay","confidenceScore":1.5763446E-05}],"entities":[{"category":"Location","text":"Sydney","offset":19,"length":6,"confidenceScore":1}]}}
    ```

8. 查看应用返回的 JSON 响应，该响应应指示针对输入预测的得分最高的意图（应为 **GetTime**）。
9. 将 curl 命令中的查询更改为 `What’s today’s date?`，然后运行它，查看生成的 JSON。
10. 请尝试以下查询：

    `What day will Jan 1st 2050 be?`

    `What time is it in Glasgow?`

    `What date will next Monday be?`

## 导出项目

可以使用 Language Studio 来开发和测试语言理解模型，但在 DevOps 的软件开发过程中，应维护可包含在持续集成和持续交付 (CI/CD) 管道中的项目的源代码控制定义。虽然可以使用代码脚本中的语言 REST API 来创建和训练模型，但一种更简单的方法是使用门户来创建模型架构，并将其导出为可在另一语言服务实例中导入和重新训练的 [*.json*] 文件。这种方法使你能够使用 Language Studio 可视化界面的工作效率优势，同时保持模型的可移植性和可再现性。

1. 在 Language Studio 中的“**项目**”页面，选择“**Clock (对话)**”项目。
2. 单击“**&#x2913; 导出**”按钮。
3. 保存生成的 **Clock.json** 文件（可保存在你喜欢的任何位置）。
4. 打开你喜欢的代码编辑器（例如 Visual Studio Code）中的已下载文件，以查看项目的 JSON 定义。

## 更多信息

如需详细了解如何使用**语言**服务创建对话语言理解解决方案，请参阅[语言服务文档](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/overview)。
