---
title: 区域 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务的可用区域和终结点列表，包括语音转文本、文本转语音和语音翻译。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b0574c41042e172af78365bb273c81729ce204ab
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749313"
---
# <a name="speech-service-supported-regions"></a>语音服务支持的区域

通过语音服务，应用程序可将音频转换为文本、执行语音翻译以及将文本转换为语音。 多个区域中均提供该服务，这些区域为语音 SDK 和 REST API 使用唯一终结点。

此处提供了对所有区域的语音体验执行自定义配置的语音门户： https://speech.microsoft.com

考虑区域时，请注意以下几点：

* 如果你的应用程序使用 [语音 SDK](speech-sdk.md)，则在 `westus` 创建语音配置时提供区域标识符，如。
* 如果应用程序使用某个语音服务的 [REST API](rest-apis.md)，则区域是你在发出请求时使用的终结点 URI 的一部分。
* 为某个区域创建的密钥仅在该区域有效。 尝试在其他区域使用此类密钥会导致身份验证错误。

## <a name="speech-sdk"></a>语音 SDK

在[语音 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中用作 `SpeechConfig.FromSubscription` 的参数）。

### <a name="speech-to-text-text-to-speech-and-translation"></a>语音转文本、文本转语音和翻译

语音自定义门户在此处提供： https://speech.microsoft.com

可以在以下区域使用语音服务，以进行**语音识别**、**文本转语音**和**翻译**：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

如果使用[语音 SDK](speech-sdk.md)，则区域由**区域标识符**指定（例如，作为 `SpeechConfig.FromSubscription` 的参数）。 请确保该区域与订阅的区域匹配。

### <a name="intent-recognition"></a>意向识别

通过语音 SDK 实现**意向识别**的可用区域如下：

| 全球区域 | 区域           | 区域标识符 |
| ------------- | ---------------- | -------------------- |
| 亚洲          | 东亚        | `eastasia`           |
| 亚洲          | 东南亚   | `southeastasia`      |
| 澳大利亚     | 澳大利亚东部   | `australiaeast`      |
| 欧洲        | 北欧     | `northeurope`        |
| 欧洲        | 西欧      | `westeurope`         |
| 北美 | 美国东部          | `eastus`             |
| 北美 | 美国东部 2        | `eastus2`            |
| 北美 | 美国中南部 | `southcentralus`     |
| 北美 | 美国中西部  | `westcentralus`      |
| 北美 | 美国西部          | `westus`             |
| 北美 | 美国西部 2        | `westus2`            |
| 南美洲 | 巴西南部     | `brazilsouth`        |

这是[语言理解服务 (LUIS)](/azure/cognitive-services/luis/luis-reference-regions) 支持的发布区域的子集。

### <a name="voice-assistants"></a>语音助手

[语音 SDK](speech-sdk.md)支持以下区域中的**语音助手**功能：

| 区域         | 区域标识符 |
| -------------- | -------------------- |
| 美国西部        | `westus`             |
| 美国西部 2      | `westus2`            |
| 美国东部        | `eastus`             |
| 美国东部 2      | `eastus2`            |
| 西欧    | `westeurope`         |
| 北欧   | `northeurope`        |
| 东南亚 | `southeastasia`      |

### <a name="speaker-recognition"></a>说话人识别

演讲者识别目前仅适用于该 `westus` 区域。

## <a name="rest-apis"></a>REST API

语音服务还为语音转文本和文本转语音请求公开 REST 终结点。

### <a name="speech-to-text"></a>语音转文本

有关语音转文本的参考文档，请参阅[语音转文本 REST API](rest-speech-to-text.md)。

REST API 的终结点具有以下格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必须将语言参数追加到 URL 以避免收到 4xx HTTP 错误。 例如，使用“美国西部”终结点设置为美国英语的语言为：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

### <a name="text-to-speech"></a>文本转语音

有关文本转语音的参考文档，请参阅[文本转语音 REST API](rest-text-to-speech.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
