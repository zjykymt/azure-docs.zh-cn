---
title: Azure Service Fabric sfctl 网格代码-包-日志
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含用于获取指定代码包日志的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257299"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
获取给定服务副本的指定代码包的容器日志。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| get | 从容器中获取日志。 |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
从容器中获取日志。

获取服务副本的指定代码包的容器日志。

### <a name="arguments"></a>自变量

|参数|说明|
| --- | --- |
| --app-name --application-name [必需] | 应用程序的名称。 |
| --code-package-name [必需] | 服务的代码包名称。 |
| --replica-name [必需] | Service Fabric 副本名称。 |
| --service-name [必需] | 服务的名称。 |
| --tail | 要从日志末尾显示的行数。 默认值为 100。 “all”表示显示完整日志。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |


## <a name="next-steps"></a>后续步骤
- [设置](service-fabric-cli.md)Service Fabric CLI。
- 了解如何通过[示例脚本](./scripts/sfctl-upgrade-application.md)使用 Service Fabric CLI。
