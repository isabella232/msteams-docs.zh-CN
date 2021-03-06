---
title: 工作组应用程序的本地化
description: 介绍有关本地化应用程序的问题
keywords: 团队发布 microsoft store office 发布 AppSource 本地化语言
ms.date: 05/15/2018
ms.openlocfilehash: 7af8018414b6ec72c45639a2a370166c24185af6
ms.sourcegitcommit: 0aeb60027f423d8ceff3b377db8c3efbb6da4d17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "48997977"
---
# <a name="localization-for-microsoft-teams-apps"></a>Microsoft 团队应用程序的本地化

在本地化 Microsoft 团队应用程序时，需要考虑三个主要方面。

1. 您的 AppSource 列出 (如果发布到应用商店) 。
1. 您的应用程序清单中面向最终用户的字符串 () 的 bot 命令示例。
1. 对从用户提交的本地化文本做出响应。

## <a name="localizing-your-appsource-listing"></a>本地化您的 AppSource 列表

如果您正在发布到应用商店，您需要注意，尚不支持本地化 AppSource 列表。 但是，在对应用商店中的本地化列表进行支持的准备过程中，可以向列表中添加其他语言。 目前，您在 [合作伙伴中心](/office/dev/store/submit-to-appsource-via-partner-center) 提供的默认 (英语) 语言信息将显示在您的应用程序的 [AppSource 网站](https://appsource.microsoft.com/marketplace/apps?product=office%3Bteams&page=1) 列表中。

若要为您的应用程序配置其他语言，请在 " [合作伙伴中心](/office/dev/store/submit-to-appsource-via-partner-center)" 中，选择 "英语" 和 "应用程序的其他语言"。 在此示例中使用的是法语。

1. 添加英语语言
    * 填写应用程序名称。
    * 使用英语填写应用程序的简短说明。
    * 用英语填写应用程序的详细说明。
    * 在详细说明中，请同时添加 "此应用程序在 ' 法语 ' 中可用" 这一行。
    * 将应用程序 UI (的图像上传) 的英文版本。
2. 添加法语语言
    * 填写应用程序名称。
    * 以法语填写应用程序的简短说明。
    * 以法语填写应用程序的详细说明。
    * 在法语) 中上载应用程序 UI (的图像。

以英语语言上传的图像将是 AppSource 中使用的图像。

## <a name="localizing-the-strings-in-your-app-manifest"></a>本地化应用程序清单中的字符串

您必须使用 Microsoft 团队应用程序架构 v1.0 + 以正确本地化您的应用程序。 为此，可以将 `$schema` manifest.js的文件中的属性设置为 " https://developer.microsoft.com/en-us/json-schemas/teams/v1.8/MicrosoftTeams.Localization.schema.json "，并将 "manifestVersion" 属性更新为 "1.7"。

### <a name="example-manifestjson-change"></a>更改 manifest.js示例

```json
{
  "$schema": "https://developer.microsoft.com/en-us/json-schemas/teams/v1.8/MicrosoftTeams.Localization.schema.json",
  "manifestVersion": "1.5",
  ...
}
```

然后，您需要使用您的应用程序支持的默认语言添加 "localizationInfo" 属性。 如果用户的客户端设置与任何其他语言都不匹配，则将默认语言用作最终回退语言。

### <a name="example-manifestjson-change"></a>更改 manifest.js示例

```json
{
  ...
  "localizationInfo": {
    "defaultLanguageTag": "en"
  }
  ...
}
```

您可以提供附加的 json 文件，其中包含清单中所有面向用户的字符串的转换。 这些文件必须遵循 [本地化文件 JSON 架构](../../resources/schema/localization-schema.md) ，并且必须将其添加到清单的 "localizationInfo" 属性。 每个文件都与一个语言标记相关联，团队客户端使用这些标记来选择适当的字符串。 语言标记采用的形式为， <language> - <region> 但建议省略 <region> 部分，以面向支持所需语言的所有区域。

团队客户端将按如下顺序应用这些字符串：默认语言字符串-> 用户的语言仅为字符串-> 用户的语言 + 用户的区域字符串。

例如，您提供了默认语言 ' fr ' (法语、所有区域) 和 ' en ' 的其他语言文件 (英语中，所有区域) 和 ' en gb ' (英语、英国) 。 如果用户的语言设置为 "en-gb"：

1. 团队客户端将使用 ' en ' 字符串将其替换为 "fr" 字符串。
2. 使用 ' en gb ' 字符串覆盖这些字符串。

如果用户的语言设置为 "en-ca"： 

1. 团队客户端将使用 ' en ' 字符串将其替换为 "fr" 字符串。
2. 由于未提供 "en-ca" 本地化，将使用 "en" localizations。

如果用户的语言设置为 "es"，则团队客户端将采用 "fr" 字符串，而不会使用任何语言文件覆盖这些字符串。

因此，强烈建议在清单中提供顶级的纯语言翻译 ( ' en ' 而不是 ' en-us ' ) 并仅为需要它们的少数几个字符串提供区域级替代。

### <a name="example-manifestjson-change"></a>更改 manifest.js示例

```json
{
  ...
  "localizationInfo": {
    "defaultLanguageTag": "en",
    "additionalLanguages": [
      {
        "languageTag": "en-gb",
        "file": "en-gb.json"
      },
      {
        "languageTag": "fr",
        "file": "fr.json"
      },
      {
        "languageTag": "pl",
        "file": "pl.json"
      }
    ]
  }
  ...
}
```

### <a name="example-localization-json-file"></a>示例本地化 json 文件

```json
{
  "$schema": "https://developer.microsoft.com/en-us/json-schemas/teams/v1.8/MicrosoftTeams.Localization.schema.json",
  "name.short": "Le App",
  "name.full": "App pour Microsoft Teams",
  "description.short": "Créez d'excellentes applications pour Microsoft Teams avec App.",
  "description.full": "Créez de nouvelles applications Microsoft Teams, concevez et prévisualisez des cartes bot, et explorez la documentation avec App.",
  "staticTabs[0].name": "Editeur de manifest",
  "staticTabs[1].name": "Editeur de cartes",
  "staticTabs[2].name": "Bibliothèque de contrôles",
  "bots[0].commandLists[0].commands[0].title": "chercher",
  "bots[0].commandLists[0].commands[0].description": "Rechercher la documentation Teams pertinente"
}
```

## <a name="handling-localized-text-submissions-from-your-users"></a>处理用户的本地化文本提交

如果您提供了应用程序的本地化版本，则您的用户很可能会使用相同的语言进行响应。 团队不会将用户的提交翻译回默认语言，因此您的应用程序将需要处理该情况。 例如，如果您提供本地化的 `commandList` ，则对你的 bot 的响应将是该命令的本地化文本，而不是默认语言。 您的应用程序需要进行相应的响应。
