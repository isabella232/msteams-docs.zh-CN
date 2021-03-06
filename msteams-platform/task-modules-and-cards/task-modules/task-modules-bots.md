---
title: 在 Microsoft 团队 bot 中使用任务模块
description: 如何在 Microsoft 团队 bot 中使用任务模块，包括机器人框架卡、自适应卡片和深层链接。
keywords: 任务模块团队 bot
ms.openlocfilehash: 32fb6a4aa0a8bf2297a4f60331dc5c6c6aceb4e2
ms.sourcegitcommit: 214eccbadb7f3a67236b79a041ef487b7bf6dfbd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "44801137"
---
# <a name="using-task-modules-from-microsoft-teams-bots"></a>使用 Microsoft 团队 bot 中的任务模块

可以使用自适应卡片和机器人框架卡（英雄、缩略图和 Office 365 连接器）上的按钮从 Microsoft 工作组 bot 调用任务模块。 作为开发人员必须跟踪 bot 状态并允许用户中断/取消序列的多个对话步骤相比，任务模块通常是更好的用户体验。

有两种方法可以调用任务模块：

* **一种新的调用消息 `task/fetch` 。** 使用 `invoke` Bot 框架卡的[卡操作](~/task-modules-and-cards/cards/cards-actions.md#invoke)或 `Action.Submit` 自适应卡片的[卡操作](~/task-modules-and-cards/cards/cards-actions.md#adaptive-cards-actions)， `task/fetch` 任务模块（URL 或自适应卡片）是从你的 Bot 动态获取的。
* **深层链接 Url。** 通过使用[任务模块的深层链接语法](~/task-modules-and-cards/what-are-task-modules.md#task-module-deep-link-syntax)，可以 `openUrl` 分别对 Bot 框架卡或卡片操作使用 "[智能卡" 操作](~/task-modules-and-cards/cards/cards-actions.md#openurl) `Action.OpenUrl` [card action](~/task-modules-and-cards/cards/cards-actions.md#adaptive-cards-actions) 。 使用深层链接 Url 时，任务模块 URL 或自适应卡片正文会提前知道，从而避免相对于服务器的往返行程 `task/fetch` 。

>[!IMPORTANT]
>为确保安全通信，每个 `url` 和 `fallbackUrl` 必须实现 HTTPS 加密协议。

## <a name="invoking-a-task-module-via-taskfetch"></a>通过任务/提取调用任务模块

当 `value` 卡片操作的对象 `invoke` 或 `Action.Submit` 以正确的方式进行初始化时（下面将详细介绍），当用户按下按钮时，会将 `invoke` 邮件发送到 bot。 在对邮件的 HTTP 响应中 `invoke` ，有一个嵌入在包装对象中的[TaskInfo 对象](~/task-modules-and-cards/what-are-task-modules.md#the-taskinfo-object)，团队使用该对象来显示任务模块。

![任务/回迁请求/响应](~/assets/images/task-module/task-module-invoke-request-response.png)

让我们更详细地了解一下每个步骤：

1. 本示例显示了带有 "购买" `invoke` [卡片操作](~/task-modules-and-cards/cards/cards-actions.md#invoke)的 Bot 框架英雄卡片。 属性的值 `type` 为 `task/fetch` -对象的其余部分 `value` 可以是您喜欢的任何内容。
2. 机器人将接收 `invoke` HTTP POST 消息。
3. Bot 将创建响应对象，并使用 HTTP 200 响应代码在 POST 响应的正文中返回该对象。 有关响应的架构[在有关任务/提交的讨论中](#the-flexibility-of-tasksubmit)进行了说明，但现在要记住的重要一点是，HTTP 响应的正文包含嵌入在包装对象中的[TaskInfo 对象](~/task-modules-and-cards/what-are-task-modules.md#the-taskinfo-object)，例如：

    ```json
    {
      "task": {
        "type": "continue",
        "value": {
          "title": "Task module title",
          "height": 500,
          "width": "medium",
          "url": "https://contoso.com/msteams/taskmodules/newcustomer",
          "fallbackUrl": "https://contoso.com/msteams/taskmodules/newcustomer"
        }
      }
    }
    ```

    `task/fetch`Bot 的事件及其响应在概念上类似于 `microsoftTeams.tasks.startTask()` 客户端 SDK 中的功能。
4. Microsoft 团队显示任务模块。

## <a name="submitting-the-result-of-a-task-module"></a>提交任务模块的结果

当用户使用任务模块完成后，将结果提交回 bot 与[使用选项卡的方式](~/task-modules-and-cards/task-modules/task-modules-tabs.md#example-submitting-the-result-of-a-task-module)类似，但存在一些不同之处，因此也会在此处进行说明。

* **HTML/JavaScript （ `TaskInfo.url` ）**。 验证用户输入的内容后，调用 `microsoftTeams.tasks.submitTask()` SDK 函数（在以后 `submitTask()` 出于可读性目的而引用）。 `submitTask()`如果只想要团队关闭任务模块，则可以不带任何参数调用，但大多数时候需要将一个对象或字符串传递给您的 `submitHandler` 。 只需将其作为第一个参数传递即可 `result` 。 团队将调用 `submitHandler` ： `err` 将为 `null` ，并 `result` 将成为您传递给的对象/字符串 `submitTask()` 。 如果 `submitTask()` 使用参数进行调用 `result` ，则**必须**传递一个或多 `appId` 个 `appId` 字符串数组：这允许团队验证发送结果的应用程序是否与调用任务模块的应用程序相同。 你的 bot 将收到一 `task/submit` 条消息，其中包括如下 `result` 所述的消息。 [below](#payload-of-taskfetch-and-tasksubmit-messages)
* **自适应卡片（ `TaskInfo.card` ）**。 自适应卡片正文（由用户填写）将在 `task/submit` 用户按下任意按钮时通过消息发送到机器人 `Action.Submit` 。

## <a name="the-flexibility-of-tasksubmit"></a>任务/提交的灵活性

在上一节中，您了解到当用户完成从 bot 中调用的任务模块后，bot 将始终收到一 `task/submit invoke` 条消息。 作为开发人员，您可以在*响应*邮件时使用以下几个选项 `task/submit` ：

| HTTP 正文响应                      | 应用场景                                |
| --------------------------------------- | --------------------------------------- |
| 无（忽略该 `task/submit` 消息） | 最简单的响应是无响应。 当用户使用任务模块完成时，你的 bot 不需要进行响应。 |
| <pre>{<br/>  "task": {<br/>    "type": "message",<br/>    "value": "Message text"<br/>  }<br/>}</pre> | 团队将 `value` 在弹出消息框中显示值。 |
| <pre>{<br/>  "task": {<br/>    "type": "continue",<br/>    "value": &lt;TaskInfo object&gt;<br/>  }<br/>}</pre> | 允许您在向导/多步骤体验中将自适应卡片的序列 "链" 在一起。 _请注意，将自适应卡片链接到一个序列是一个高级方案，此处未对其进行介绍。但是，Node.js 示例应用程序支持它，并且它的工作方式在[其 README.md 文件](https://github.com/OfficeDev/microsoft-teams-sample-task-module-nodejs#implementation-notes)中进行了记录。_ |

## <a name="payload-of-taskfetch-and-tasksubmit-messages"></a>任务/提取和任务/提交邮件的有效负载

此部分定义了你的 bot 在接收 `task/fetch` 或 `task/submit` bot 框架对象时接收的内容的架构 `Activity` 。 下面显示了重要的顶级：

| 属性 | 说明                          |
| -------- | ------------------------------------ |
| `type`   | 将始终为`invoke`              |
| `name`   | `task/fetch`或者`task/submit` |
| `value`  | 开发人员定义的有效负载。 通常，对象的结构会 `value` 反映从团队发送的内容。 但是，在这种情况下，由于我们要支持 `task/fetch` 来自 Bot 框架（ `value` ）和自适应卡片操作（）的动态提取（） `Action.Submit` `data` ，并且 `context` 除了中包含的内容之外，我们还需要一种方法来将团队与 bot 进行通信 `value` / `data` 。<br/><br/>为此，我们将两者组合成一个父对象：<br/><br/><pre>{<br/>  "context": {<br/>    "theme": "default" &vert; "dark" &vert; "contrast",<br/>  },<br/>  "data": [value field from Bot Framework card] &vert; [data field from Adaptive Card] <br/>}</pre>  |

## <a name="example-receiving-and-responding-to-taskfetch-and-tasksubmit-invoke-messages---nodejs"></a>示例：接收和响应任务/提取和任务/提交调用邮件-Node.js

> [!NOTE]
> 下面的示例代码在此功能的技术预览和最终版本中进行了修改：请求的架构 `task/fetch` 已更改，以遵循[上一节中所述](#payload-of-taskfetch-and-tasksubmit-messages)内容。 也就是说，文档是正确的，但实现不正确。 请参阅 `// for Technical Preview [...]` 下面的注释以了解所做的更改。

```typescript
// Handle requests and responses for a "Custom Form" and an "Adaptive card" task module.
// Assumes request is coming from an Adaptive card Action.Submit button that has a "taskModule" property indicating what to invoke
private async onInvoke(event: builder.IEvent, cb: (err: Error, body: any, status?: number) => void): Promise<void> {
    let invokeType = (event as any).name;
    let invokeValue = (event as any).value;
    if (invokeType === undefined) {
        invokeType = null;
    }
    switch (invokeType) {
        case "task/fetch": {
            if (invokeValue !== undefined && invokeValue.data.taskModule === "customform") { // for Technical Preview, was invokeValue.taskModule
                // Return the specified task module response to the bot
                let fetchTemplate: any = {
                    "task": {
                        "type": "continue",
                        "value": {
                            "title": "Custom Form",
                            "height": 510,
                            "width": 430,
                            "fallbackUrl": "https://contoso.com/teamsapp/customform",
                            "url": "https://contoso.com/teamsapp/customform",
                        }
                    }
                };
                cb(null, fetchTemplate, 200);
            };
            if (invokeValue !== undefined && invokeValue.data.taskModule === "adaptivecard") { // for Technical Preview, was invokeValue.taskModule
                let adaptiveCard = {
                    "type": "AdaptiveCard",
                    "body": [
                        {
                            "type": "TextBlock",
                            "text": "Here is a ninja cat:"
                        },
                        {
                            "type": "Image",
                            "url": "http://adaptivecards.io/content/cats/1.png",
                            "size": "Medium"
                        }
                    ],
                    "version": "1.0"
                };
                // Return the specified task module response to the bot
                let fetchTemplate: any = {
                    "task": {
                        "type": "continue",
                        "value": {
                            "title": "Ninja Cat",
                            "height": "small",
                            "width": "small",
                            "card": {
                                contentType: "application/vnd.microsoft.card.adaptive",
                                content: adaptiveCard,
                            }
                        }
                    }
                };
                cb(null, fetchTemplate, 200);
            };
            break;
        }
        case "task/submit": {
            if (invokeValue.data !== undefined) {
                // It's a valid task module response
                let submitResponse: any = {
                    "task": {
                        "type": "message",
                        "value": "Task complete!",
                    }
                };
                cb(null, fetchTemplates.submitMessageResponse, 200)
            }
        }
    }
}
```

*另*请参阅[Microsoft 团队任务模块示例代码-Nodejs](https://github.com/OfficeDev/microsoft-teams-sample-task-module-nodejs/blob/master/src/TeamsBot.ts)和[Bot 框架示例](https://github.com/Microsoft/BotBuilder-Samples/blob/master/README.md)。

## <a name="example-receiving-and-responding-to-taskfetch-and-tasksubmit-invoke-messages---c"></a>示例：接收和响应任务/提取和任务/提交调用消息-C#

在 c # bot 中， `invoke` 处理邮件的控制器会处理邮件 `HttpResponseMessage()` `Activity` 。 `task/fetch`和 `task/submit` 请求和响应为 JSON。 在 c # 中，不像处理 Node.js 中的原始 JSON 那样方便，因此，您需要使用包装类来处理 JSON 和 JSON 的序列化。 在 Microsoft 团队[c # SDK](https://www.nuget.org/packages/Microsoft.Bot.Connector.Teams)中，尚无直接支持此功能，但您可以在[c # 示例应用](https://github.com/OfficeDev/microsoft-teams-sample-task-module-csharp/blob/master/Microsoft.Teams.Samples.TaskModule.Web/Models/TaskModel.cs)中看到这些简单包装类的外观示例。

下面的示例代码是 `task/fetch` `task/submit` 使用这些包装类（，）的 c # 中的示例代码和消息 `TaskInfo` `TaskEnvelope` ，[示例](https://github.com/OfficeDev/microsoft-teams-sample-task-module-csharp/blob/master/Microsoft.Teams.Samples.TaskModule.Web/Controllers/MessagesController.cs)中的 excerpted：

```csharp
private HttpResponseMessage HandleInvokeMessages(Activity activity)
{
    var activityValue = activity.Value.ToString();
    if (activity.Name == "task/fetch")
    {
        var action = Newtonsoft.Json.JsonConvert.DeserializeObject<Models.BotFrameworkCardValue<string>>(activityValue);

        Models.TaskInfo taskInfo = GetTaskInfo(action.Data);
        Models.TaskEnvelope taskEnvelope = new Models.TaskEnvelope
        {
            Task = new Models.Task()
            {
                Type = Models.TaskType.Continue,
                TaskInfo = taskInfo
            }
        };
        return Request.CreateResponse(HttpStatusCode.OK, taskEnvelope);
    }
    else if (activity.Name == "task/submit")
    {
        ConnectorClient connector = new ConnectorClient(new Uri(activity.ServiceUrl));
        Activity reply = activity.CreateReply("Received = " + activity.Value.ToString());
        connector.Conversations.ReplyToActivity(reply);
    }
    return new HttpResponseMessage(HttpStatusCode.Accepted);
}

// Helper function for building the TaskInfo object based on the incoming request
private static Models.TaskInfo GetTaskInfo(string actionInfo)
{
    Models.TaskInfo taskInfo = new Models.TaskInfo();
    switch (actionInfo)
    {
        case TaskModuleIds.YouTube:
            taskInfo.Url = taskInfo.FallbackUrl = ApplicationSettings.BaseUrl + "/" + TaskModuleIds.YouTube;
            SetTaskInfo(taskInfo, TaskModuleUIConstants.YouTube);
            break;
        case TaskModuleIds.PowerApp:
            taskInfo.Url = taskInfo.FallbackUrl = ApplicationSettings.BaseUrl + "/" + TaskModuleIds.PowerApp;
            SetTaskInfo(taskInfo, TaskModuleUIConstants.PowerApp);
            break;
        case TaskModuleIds.CustomForm:
            taskInfo.Url = taskInfo.FallbackUrl = ApplicationSettings.BaseUrl + "/" + TaskModuleIds.CustomForm;
            SetTaskInfo(taskInfo, TaskModuleUIConstants.CustomForm);
            break;
        case TaskModuleIds.AdaptiveCard:
            taskInfo.Card = AdaptiveCardHelper.GetAdaptiveCard();
            SetTaskInfo(taskInfo, TaskModuleUIConstants.AdaptiveCard);
            break;
        default:
            break;
    }
    return taskInfo;
}
```

上面的示例中未显示 `SetTaskInfo()` 函数，用于设置 `height` `width` `title` `TaskInfo` 每种情况下对象的、和属性。 以下是[SetTaskInfo （）的源代码](https://github.com/OfficeDev/microsoft-teams-sample-task-module-csharp/blob/master/Microsoft.Teams.Samples.TaskModule.Web/Controllers/MessagesController.cs)。

### <a name="bot-framework-card-actions-vs-adaptive-card-actionsubmit-actions"></a>Bot 框架卡片操作与自适应卡片操作。提交操作

机器人框架卡片操作的架构与自适应卡片操作略有不同 `Action.Submit` 。 因此，调用任务模块的方法也稍有不同： `data` 对象 `Action.Submit` 包含一个 `msteams` 对象，因此它不会干扰卡片中的其他属性。 下表显示了每个示例：

| 机器人框架卡操作                              | 自适应卡片操作。提交操作                     |
| ------------------------------------------------------ | ------------------------------------------------------ |
| <pre>{<br/>  "type": "invoke",<br/>  "title": "Buy",<br/>  "value": {<br/>    "type": "task/fetch",<br/>    &lt;...&gt;<br/>  }<br/>}</pre> | <pre>{<br/>  "type": "Action.Submit",<br/>  "id": "btnBuy",<br/>  "title": "Buy",<br/>  "data": {<br/>    &lt;...&gt;,<br/>    "msteams": {<br/>      "type": "task/fetch"<br/>    }<br/>  }<br/>}</pre>  |
