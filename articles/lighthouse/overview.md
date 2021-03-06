---
title: 什么是 Azure Lighthouse？
description: 借助 Azure Lighthouse，服务提供商可为其客户提供自动化程度更高且更高效的规模级托管服务。
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: d21d11d1316b177f38b6bce197dec4f2c3d3fac8
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660727"
---
# <a name="what-is-azure-lighthouse"></a>什么是 Azure Lighthouse？

Azure Lighthouse 允许跨租户和多租户管理，允许跨资源和租户提升自动化程度、可伸缩性并增强管理。 借助 Azure Lighthouse，服务提供商可以使用 Azure 平台内置的全面而强大的管理工具来提供托管服务。 此产品/服务还可让企业 IT 组织跨多个租户管理资源。

![Azure Lighthouse 概述示意图](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>优点

Azure Lighthouse 有助于你更合理且更有效地构建和交付托管服务。 优点包括：

- **大规模管理**：用于管理客户资源的客户参与和生命周期操作更加简单且更具可缩放性。 现有的 API、管理工具和工作流可以与委托的资源配合使用，而不管资源位于哪个区域。
- **为客户提供更好的可见性和准确度**：客户会更清楚地了解你的操作，并可精确控制其委托管理的范围，包括在保留 IP 的同时完全删除访问权限的功能。
- **全面统一的平台工具**：我们的工具体验解决了重要的服务提供商方案，其中包括多种许可模型，如 EA、CSP 和即用即付。 新功能适用于现有工具和 API、许可模型和合作伙伴计划，如[云解决方案提供商计划 (CSP)](/partner-center/csp-overview)。 Azure Lighthouse 可集成到现有的工作流和应用程序中，你可以通过[关联合作伙伴 ID](../cost-management-billing/manage/link-partner-id.md) 来跟踪你对客户参与的影响。

使用 Azure Lighthouse 管理 Azure 资源不会产生额外的费用。 任何 Azure 客户或合作伙伴都可以使用 Azure Lighthouse。

## <a name="capabilities"></a>功能

Azure Lighthouse 提供了多种方法来协助简化参与和管理：

- **Azure 委派资源管理**：无需切换上下文和控制平面即可从自己的租户中安全地管理客户的 Azure 资源。 可以将订阅和资源组委托给管理租户中的指定用户和角色，并可以根据需要删除访问权限。 有关详细信息，请参阅 [Azure 委托资源管理](concepts/azure-delegated-resource-management.md)。
- **新 Azure 门户体验**：在 Azure 门户中的[“我的客户”页面](./how-to/view-manage-customers.md)中查看跨租户信息。 使用相应的[“服务提供商”页面](how-to/view-manage-service-providers.md)，客户可以查看和管理其服务提供商访问。
- **Azure 资源管理器模板**：我们的模板可帮助执行跨租户管理任务和载入委派的客户资源。 有关详细信息，请参阅我们的[存储库示例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates)和[将客户载入到 Azure Lighthouse](how-to/onboard-customer.md)。
- **Azure 市场中的托管服务产品**：通过专用或公开产品为客户提供服务，并将其自动载入 Azure Lighthouse。 有关详情，请参阅 [Azure 市场中的托管服务产品](concepts/managed-services-offers.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委派资源管理](concepts/azure-delegated-resource-management.md)。
- 了解[跨租户管理体验](concepts/cross-tenant-management-experience.md)。
- 了解如何[在企业内使用 Azure Lighthouse](concepts/enterprise.md)。
