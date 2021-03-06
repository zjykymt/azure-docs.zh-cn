---
author: baanders
description: 包括用于清理基本 Azure 数字孪生实例和应用注册的文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891483"
---
如果不再需要本教程中创建的资源，请按照以下步骤将其删除。

利用 [Azure Cloud Shell](https://shell.azure.com)，你可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令删除资源组中的所有 Azure 资源。 这将删除资源组和 Azure 数字孪生实例。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

打开 Azure Cloud Shell 并运行以下命令，以删除资源组及其包含的所有内容。

```azurecli-interactive
az group delete --name <your-resource-group>
```

接下来，使用以下命令删除为客户端应用创建的 Azure Active Directory 应用注册：

```azurecli-interactive
az ad app delete --id <your-application-ID>
```