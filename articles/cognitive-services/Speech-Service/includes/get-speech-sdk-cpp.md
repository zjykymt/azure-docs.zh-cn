---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399948"
---
:::row:::
    :::column span="3":::
        C + + 语音 SDK 在 Windows、Linux 和 macOS 上可用。 有关详细信息，请参阅<a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">cognitiveservices account <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C + + NuGet 包

C + + 语音 SDK 可通过以下`Install-Package`命令从**包管理器**安装。

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C + + 二进制文件和头文件

或者，可以从二进制文件中安装 c + + 语音 SDK。 将 SDK 下载为<a href="https://aka.ms/csspeech/linuxbinary" target="_blank">tar 包<span class="docon docon-navigate-external x-hidden-focus"></span> </a> ，并将文件解压缩到所选的目录中。 此包的内容（包括 x86 和 x64 目标体系结构的标头文件）的结构如下所示：

  | 路径                   | 说明                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | 许可                                              |
  | `ThirdPartyNotices.md` | 第三方声明                                  |
  | `include`              | C + + 头文件                                 |
  | `lib/x64`              | 用于与应用程序链接的本机 x64 库 |
  | `lib/x86`              | 用于与应用程序链接的本机 x86 库 |

  要创建应用程序，请将必需的二进制文件（以及库）复制到开发环境中。 在生成过程中根据需要添加它们。

#### <a name="additional-resources"></a>其他资源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows、Linux 和 macOS 快速入门 c + + 源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>