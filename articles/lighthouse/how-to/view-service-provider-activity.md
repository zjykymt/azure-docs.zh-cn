---
title: 查看服务提供商活动
description: 客户可以查看记录的活动，查看服务提供商通过 Azure 委派的资源管理执行的操作。
ms.date: 07/07/2020
ms.topic: how-to
ms.openlocfilehash: 0c92fc9b45d17e37fb3721d9cf087c5e7a62f6d7
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131476"
---
# <a name="view-service-provider-activity"></a>查看服务提供商活动

已委派[Azure Lighthouse](../overview.md)订阅的客户可以[查看 azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)数据，查看所执行的所有操作。 这使客户能够全面了解服务提供商通过[Azure 委派的资源管理](../concepts/azure-delegated-resource-management.md)执行的操作，以及客户自己 Azure Active Directory （Azure AD）租户中的用户完成的操作。

> [!TIP]
> 我们还提供了 Azure 策略内置策略定义，用于审核作用域到管理租户的委派。 有关详细信息，请参阅[在你的环境中审核委派](view-manage-service-providers.md#audit-delegations-in-your-environment)。

## <a name="view-activity-log-data"></a>查看活动日志数据

你可以从 "**监视**" 菜单中的 "Azure 门户"[查看活动日志](../../azure-monitor/platform/activity-log.md#view-the-activity-log)。 若要将结果限制为特定的订阅，请使用筛选器选择特定订阅。 您还可以通过编程方式[查看和检索活动日志事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)。

> [!NOTE]
> 如果服务提供商的租户中的用户已被授予 "[读取](../../role-based-access-control/built-in-roles.md#reader)者" 角色（或另一个包含读者访问权限的内置角色），则在该订阅被载入到 Azure Lighthouse 时，服务提供商的租户中的用户可以查看该订阅的活动日志结果。

在活动日志中，你将看到操作的名称及其状态，以及执行该操作的日期和时间。 "**按启动的事件**" 列显示哪个用户执行了该操作，无论是服务提供商的租户中的用户是通过 Azure Lighthouse 还是客户自己的租户中的用户。 请注意，将显示用户的名称，而不是该用户分配给该订阅的租户或角色。

记录的活动在过去90天的 Azure 门户中可用。 若要了解如何存储超过90天的此数据，请参阅[在 Log Analytics 工作区中收集和分析 Azure 活动日志](../../azure-monitor/platform/activity-log.md)。

> [!NOTE]
> 服务提供商提供的用户出现在活动日志中，但这些用户及其角色分配不会显示在**访问控制（IAM）** 中或通过 api 检索角色分配信息。

## <a name="set-alerts-for-critical-operations"></a>为关键操作设置警报

要了解服务提供商（或你自己的租户中的用户）正在执行的关键操作，建议创建[活动日志警报](../../azure-monitor/platform/activity-log-alerts.md)。 例如，你可能想要跟踪订阅的所有管理操作，或在删除特定资源组中的任何虚拟机时通知你。 创建警报时，它们将包括客户自己的租户中的用户执行的操作以及任何管理租户中的操作。

有关详细信息，请参阅[创建和管理活动日志警报](../../azure-monitor/platform/alerts-activity-log.md)。

## <a name="create-log-queries"></a>创建日志查询

你可以创建查询来分析你记录的活动或关注特定的项目。 例如，可能审核要求您报告对订阅执行的所有管理级别的操作。 您可以创建一个查询来仅筛选这些操作并按用户、日期或其他值对结果进行排序。

有关详细信息，请参阅[Azure Monitor 中的日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor](../../azure-monitor/index.yml)。
- 了解如何在 Azure 门户中[查看和管理服务提供商产品/服务](view-manage-service-providers.md)。
