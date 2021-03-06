---
title: 使用 REST 管理用户分配的托管标识 - Azure AD
description: 分步说明如何创建、列出和删除用户分配托管标识以进行 REST API 调用。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f04eb0eb3741a388b375cf7d8c6455bfd4a17b39
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266334"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>使用 REST API 调用创建、列出或删除用户分配托管标识

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure 资源托管标识使 Azure 服务能够向支持 Azure AD 身份验证的服务进行身份验证，而无需在代码中输入凭据。 

本文介绍如何使用 CURL 创建、列出和删除用户分配托管标识以进行 REST API 调用。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 如果使用 Windows，请安装[适用于 Linux 的 Windows 子系统](/windows/wsl/about)或使用 Azure 门户中的 [Azure Cloud Shell](../../cloud-shell/overview.md)。
- 如果使用[适用于 Linux 的 Windows 子系统](/windows/wsl/about)或 [Linux 分发版 OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)，请[安装 Azure CLI 本地控制台](/cli/azure/install-azure-cli)。
- 如果使用 Azure CLI 本地控制台，请使用 `az login` 和与要用于部署或检索用户分配托管标识信息的 Azure 订阅关联的帐户登录 Azure。
- 使用 `az account get-access-token` 检索持有者访问令牌，进而执行以下用户分配托管标识操作。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**请求标头**

|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        |

**请求正文**

|名称  |说明  |
|---------|---------|
|location     | 必需。 资源位置。        |

## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出/读取用户分配的托管标识，你的帐户需要[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        |

## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

> [!NOTE]
> 删除用户分配托管标识不会从将其分配到的任何资源中删除引用。 要使用 CURL 从 VM 中删除用户分配的托管标识，请参阅[从 Azure VM 中删除用户分配的标识](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm)。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        |

## <a name="next-steps"></a>后续步骤

要了解如何使用 CURL 将用户分配托管标识分配给 Azure VM/VMSS，请参阅[使用 REST API 调用在 Azure VM 上配置 Azure 资源托管标识](qs-configure-rest-vm.md#user-assigned-managed-identity)和[使用 REST API 调用在虚拟机规模集上配置 Azure 资源托管标识](qs-configure-rest-vmss.md#user-assigned-managed-identity)。