---
title: 超出部署配额
description: 介绍如何解决这样一个错误：在资源组历史记录中有 800 多个部署。
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 8996d7817eea2f8daf44fbc9b4416c884b05940f
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987046"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>解决部署计数超出 800 的错误

每个资源组在其部署历史记录中最多只能有 800 个部署。 本文介绍在部署因超出允许的 800 个部署的限制而失败时可能会出现的错误。 若要解决此错误，请从资源组历史记录中删除部署。 从历史记录中删除部署不会影响已部署的任何资源。

Azure 资源管理器会自动删除历史记录中的部署。 由于以下原因之一，你可能仍会看到此错误：

1. 资源组上的 CanNotDelete 锁会阻止从部署历史记录中删除。
1. 你选择了自动删除。
1. 同时运行大量部署，并且自动删除的处理速度不够快，无法减少总数量。

有关删除锁定或选择自动删除的信息，请参阅[从部署历史记录中自动删除](deployment-history-deletions.md)。

本文介绍如何从历史记录中手动删除部署。

## <a name="symptom"></a>症状

在部署过程中，你收到错误，该错误指出当前部署会超出 800 个部署的配额。

## <a name="solution"></a>解决方案

### <a name="azure-cli"></a>Azure CLI

使用 [az deployment group delete](/cli/azure/group/deployment) 命令从历史记录中删除部署。

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

若要删除五天以前的所有部署，请使用：

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

可使用以下命令获取部署历史记录中的当前计数：

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 命令从历史记录中删除部署。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

若要删除五天以前的所有部署，请使用：

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

可使用以下命令获取部署历史记录中的当前计数：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>第三方解决方案

以下外部解决方案解决特定场景的问题：

* [Azure 逻辑应用和 PowerShell 解决方案](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps 扩展](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
