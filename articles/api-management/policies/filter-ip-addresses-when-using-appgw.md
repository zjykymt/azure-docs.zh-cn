---
title: 示例 API 管理策略 - 使用应用程序网关时对 IP 地址进行筛选
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何在使用应用程序网关时对请求 IP 地址进行筛选。
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75942472"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>使用应用程序网关时对请求 IP 地址进行筛选

本文展示了一个 Azure API 管理策略示例，该示例演示了当通过应用程序网关或其他中介访问 API 管理实例时，如何筛选请求 IP 地址。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [访问限制策略](../api-management-access-restriction-policies.md)
+ [策略示例](../policy-samples.md)
