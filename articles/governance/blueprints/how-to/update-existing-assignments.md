---
title: 从门户更新现有分配
description: 了解在 Azure 蓝图中通过门户更新现有蓝图分配的机制。
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 888ebbf0149f8f75f867bb17115988cb20d25df2
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051417"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>如何更新现有蓝图分配

分配蓝图时可更新分配。 众多原因导致要更新现有分配，其中包括：

- 添加或删除[资源锁定](../concepts/resource-locking.md)
- 更改[动态参数](../concepts/parameters.md#dynamic-parameters)的值
- 将分配升级到新发布的蓝图版本****

## <a name="updating-assignments"></a>更新分配

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 从左侧页面选择“分配的蓝图”。

1. 在蓝图列表中，选择蓝图分配。 然后，使用 " **更新分配** " 按钮或右键单击蓝图分配，然后选择 " **更新分配**"。

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="更新现有蓝图分配" border="false":::

1. " **分配蓝图** " 页加载已预先填充原始赋值的所有值。 可以更改“蓝图定义版本”****、“锁定分配”**** 状态，以及蓝图定义上存在的任何动态参数。 完成更改后，选择 " **分配** "。

1. 在更新后的分配详细信息页上，查看新状态。 在此示例中，我们向分配添加了“锁定”****。

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="更新了现有蓝图分配锁模式" border="false":::

1. 使用下拉菜单浏览有关其他 **分配操作** 的详细信息。 **托管资源**的表由所选的分配操作更新。

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="蓝图赋值的赋值运算" border="false":::

## <a name="rules-for-updating-assignments"></a>更新分配规则

更新分配部署遵循几个重要规则。 这些规则决定了已部署的资源会发生什么情况。 所请求的更改和要部署或更新的项目资源决定了要采取的操作。

- 角色分配
  - 如果角色或角色代理人（用户、组或应用程序）发生更改，则创建新的角色分配。 以前部署的角色分配将会保留。
- 策略分配
  - 如果策略分配的参数已发生更改，则更新现有分配。
  - 如果策略分配的定义已发生更改，则会创建一个新的策略分配。
    以前部署的策略分配将会保留。
  - 如果从蓝图中删除策略分配项目，已部署的策略分配将会保留。
- Azure 资源管理器模板（ARM 模板）
  - 该模板通过资源管理器作为 PUT**** 处理。 由于每个资源类型以不同的方式处理此操作，因此请查看包含的每个资源的文档，以确定在蓝图运行时该操作的影响。

## <a name="possible-errors-on-updating-assignments"></a>更新分配上可能出现的错误

更新分配时，在执行期间进行更改可能会导致中断。 一个示例是在部署完成后更改资源组的位置。 [资源管理器](../../../azure-resource-manager/management/overview.md)支持的任何更改都可以进行，但通过资源管理器导致错误的任何更改也会导致分配失败。

可以更新分配的次数没有限制。 如果发生错误，请确定该错误并对分配进行其他更新。  示例错误场景：

- 不正确的参数
- 已经存在的对象
- 资源管理器不支持的更改

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。