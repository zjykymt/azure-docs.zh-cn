---
title: Azure Service Fabric CLI sfctl 网格 secretvalue
description: 了解 sfctl，Azure Service Fabric 命令行界面。 包含用于获取和删除 Service Fabric 网格 secretvalue 资源的命令的列表。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245766"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
获取和删除网格机密值资源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| 删除 | 删除已命名机密资源的指定值。 |
| list | 列出指定的机密资源的所有值的名称。 |
| show | 列出机密资源的指定值。 |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
删除已命名机密资源的指定值。

删除由名称标识的机密值资源。 资源的名称通常是与该值关联的版本。 如果指定的值在使用中，则删除将失败。

### <a name="arguments"></a>自变量

|参数|说明|
| --- | --- |
| --secret-name -n [必需] | 机密资源的名称。 |
| --version -v     [必需] | 机密版本的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
列出指定的机密资源的所有值的名称。

获取指定的机密资源的所有机密值资源的信息。 该信息包括机密值资源的名称，但不包括实际值。

### <a name="arguments"></a>自变量

|参数|说明|
| --- | --- |
| --secret-name -n [必需] | 机密资源的名称。 |

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug | 提高日志记录详细程度以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 获取完整的调试日志。 |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
列出机密资源的指定值。

### <a name="arguments"></a>自变量

|参数|说明|
| --- | --- |
| --secret-name -n [必需] | 机密资源的名称。 |
| --version -v     [必需] | 机密版本的名称。 |
| --show-value | 显示机密版本的实际值。 |

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
