---
title: 为 Azure 管理访问设置 Azure 角色
titleSuffix: Azure Cognitive Search
description: 基于角色的访问控制 (RBAC) 在 Azure 门户中用于控制和委托 Azure 认知搜索管理管理任务。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: f0c8fe6b8df5efef0cf3948c8d628d20c79502ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928677"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>设置 azure 角色以实现 Azure 认知搜索的管理访问权限

对于通过门户或 Resource Manager API 管理的所有服务，Azure 提供了[基于全局角色的授权模型](../role-based-access-control/role-assignments-portal.md)。 所有者、参与者和读者角色根据分配给每个角色的 Active Directory 用户、组和安全主体的服务管理，确定服务管理的级别。 

> [!Note]
> 没有基于角色的访问控制 (RBAC) 来保护索引部分或部分文档。 如果要实现针对搜索结果的、基于标识的访问，可创建安全筛选器按标识来细化结果，由此去除请求者不应具有访问权限的那些文档。 有关详细信息，请参阅[安全筛选器](search-security-trimming-for-azure-search.md)和[使用 Active Directory 进行保护](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="management-tasks-by-role"></a>按角色划分的管理任务

对于 Azure 认知搜索，角色与支持以下管理任务的权限级别相关联：

| 角色 | 任务 |
| --- | --- |
| 所有者 |创建或删除服务或者服务上的任何对象，包括 API 密钥、索引、索引器、索引器数据源和索引器计划。<p>查看服务状态，包括计数和存储大小。<p>添加或删除角色成员身份（仅所有者才能管理角色成员身份）。<p>订阅管理员和服务所有者拥有所有者角色的自动成员身份。 |
| 参与者 |与所有者相同的访问级别，减去 Azure 角色管理。 例如，参与者可创建或删除对象，或查看和重新生成 [API 密钥](search-security-api-keys.md)，但不能修改角色成员身份。 |
| [搜索服务参与者内置角色](../role-based-access-control/built-in-roles.md#search-service-contributor) | 等效于参与者角色。 |
| 读取器 |查看服务概要和指标。 此角色的成员无法查看索引、索引器、数据源或密钥信息。  |

角色不授予对服务终结点的访问权限。 搜索服务操作（例如索引管理、索引填充和搜索数据的查询）可通过 API 密钥而非角色进行控制。 有关详细信息，请参阅[管理 API 密钥](search-security-api-keys.md)。

## <a name="permissions-table"></a>权限表

下表概述了 Azure 认知搜索中允许的操作，以及哪个密钥可以解锁特定操作的访问。

| 操作 | 权限 |
|-----------|-------------------------|
| 创建服务 | Azure 订阅持有者 |
| 缩放服务 | 资源中的管理密钥、RBAC 所有者或参与者  |
| 删除服务 | 资源中的管理密钥、RBAC 所有者或参与者 |
| 创建、修改、删除服务中的对象： <br>索引和组件部分（包括分析器定义、评分配置文件、CORS 选项）、索引器、数据源、同义词、建议器 | 资源中的管理密钥、RBAC 所有者或参与者 |
| 查询索引 | 管理密钥或查询密钥（RBAC 不适用） |
| 查询系统信息，例如返回统计信息、计数和对象列表 | 管理密钥，资源的 RBAC（所有者、参与者、读取者） |
| 管理管理密钥 | 管理密钥，资源中的 RBAC 所有者或参与者 |
| 管理查询密钥 |  管理密钥，资源中的 RBAC 所有者或参与者  |

## <a name="see-also"></a>另请参阅

+ [使用 PowerShell 管理](search-manage-powershell.md) 
+ [Azure 认知搜索中的性能和优化](search-performance-optimization.md)
+ [Azure 门户中基于角色的访问控制入门](../role-based-access-control/overview.md)。