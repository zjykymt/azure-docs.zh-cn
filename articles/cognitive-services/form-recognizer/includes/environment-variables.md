---
title: 环境变量
description: 设置环境变量
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: d63e902a59411a549235c955a39d7dbc4be068ba
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156636"
---
从创建的资源使用密钥和终结点，创建两个用于身份验证的环境变量：

* `FORM_RECOGNIZER_KEY` - 用于验证请求的资源密钥。
* `FORM_RECOGNIZER_ENDPOINT` - 用于发送 API 请求的资源终结点。 它将如下所示： 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

请按照以下说明中的步骤设置操作系统的环境变量。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

添加环境变量后，请关闭并重新打开控制台窗口。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macos"></a>[macOS](#tab/unix)

编辑 `.bash_profile`，然后添加环境变量：

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。
***
