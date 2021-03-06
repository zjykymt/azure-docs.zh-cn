---
title: 使用托管标识进行身份验证
description: 访问受 Azure Active Directory 保护的资源，无需使用凭据或机密使用托管标识登录
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 95d892bf7a0c0e395289d4a5535cd9b6b789b055
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565921"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的托管标识对 Azure 资源的访问进行身份验证

若要在不登录的情况下轻松访问受 Azure Active Directory (Azure AD) 保护的其他资源并对你的标识进行身份验证，逻辑应用可以使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)（以前称为托管服务标识或 MSI），而非使用凭据或密码。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。

Azure 逻辑应用支持[系统分配的](../active-directory/managed-identities-azure-resources/overview.md)和[用户分配](../active-directory/managed-identities-azure-resources/overview.md)托管标识。 逻辑应用可以使用系统分配的标识，也可以使用单个用户分配的标识，可在一组逻辑应用之间共享，但不能同时使用两者。 目前，只有[特定内置触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)支持托管标识，而不支持托管连接器或连接，例如：

* HTTP
* Azure Functions
* Azure API 管理
* Azure 应用服务

本文介绍如何为逻辑应用设置这两种类型的托管标识。 有关详细信息，请参阅以下主题：

* [支持托管标识的触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [出站调用上支持的身份验证类型](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [逻辑应用的托管标识限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [支持通过托管标识进行 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 托管标识和需要访问的目标 Azure 资源必须使用相同的 Azure 订阅。

* 若要为托管标识提供对 Azure 资源的访问权限，需要为该标识向目标资源添加一个角色。 若要添加角色，你需要 [Azure AD 管理员权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)，可以将角色分配给相应的 Azure AD 租户中的标识。

* 要访问的目标 Azure 资源。 在此资源上，你将为托管标识添加一个角色，该角色可帮助逻辑应用对目标资源的访问进行身份验证。

* 要在其中使用[支持托管标识的触发器或操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)的逻辑应用

## <a name="enable-managed-identity"></a>启用托管标识

若要设置要使用的托管标识，请单击该标识的链接：

* [系统分配的标识](#system-assigned)
* [用户分配的标识](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>启用系统分配的标识

不同于用户分配的标识，无需手动创建系统分配的标识。 若要为逻辑应用设置系统分配的标识，可以使用以下选项：

* [Azure 门户](#azure-portal-system-logic-app)
* [Azure 资源管理器模板](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 门户中启用系统分配的标识

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“标识” 。 选择“系统分配” > “开启” > “保存”。   当 Azure 提示你进行确认时，选择“是”。

   ![启用系统分配的标识](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > 如果收到一个错误，指出你只能有一个托管标识，则表明逻辑应用已与用户分配的标识相关联。 在添加系统分配的标识之前，必须先从逻辑应用中删除用户分配的标识。

   逻辑应用现在可以使用系统分配的标识，该标识注册到 Azure Active Directory，由对象 ID 表示。

   ![系统分配的标识的对象 ID](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | properties | 值 | 说明 |
   |----------|-------|-------------|
   | **对象 ID** | <*identity-resource-ID*> | 全局唯一标识符 (GUID)，表示 Azure AD 租户中逻辑应用的系统分配的标识 |
   ||||

1. 现在，按照本主题后面的[授予该标识对资源的访问权限](#access-other-resources)的步骤进行操作。

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中启用系统分配的标识

若要自动创建和部署 Azure 资源（如逻辑应用），可以使用 [Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 若要在模板中为逻辑应用启用系统分配的托管标识，请在模板中将 `identity` 对象和 `type` 子属性添加到逻辑应用的资源定义中，例如：

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

当 Azure 创建逻辑应用资源定义时，`identity` 对象获取这些附加属性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 属性 (JSON) | 值 | 说明 |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | 托管标识的服务主体对象的全局唯一标识符 (GUID)，表示 Azure AD 租户中的逻辑应用。 此 GUID 有时显示为“对象 ID”或 `objectID`。 |
| `tenantId` | <*Azure-AD-tenant-ID*> | 全局唯一标识符 (GUID)，表示逻辑应用现在是其中的一名成员的 Azure AD 租户。 在 Azure AD 租户内，服务主体与逻辑应用实例具有相同名称。 |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>启用用户分配的标识

若要为逻辑应用设置用户分配的托管标识，必须首先将该标识创建为独立的 Azure 资源。 以下是可使用的选项：

* [Azure 门户](#azure-portal-user-identity)
* [Azure 资源管理器模板](#template-user-identity)
* Azure PowerShell
  * [创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [添加角色分配](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [添加角色分配](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [添加角色分配](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>在 Azure 门户中创建用户分配的标识

1. 在 [Azure 门户](https://portal.azure.com)的任意页面上的搜索框中，输入 `managed identities`，然后选择“托管标识”。

   ![查找并选择“托管标识”](./media/create-managed-service-identity/find-select-managed-identities.png)

1. 在“托管标识”下，选择“添加”。 

   ![添加新的托管标识](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 提供有关托管标识的信息，然后选择“创建”，例如：

   ![创建用户分配的托管标识](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | properties | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | 资源名称 | 是 | <*user-assigned-identity-name*> | 赋予用户分配的标识的名称。 此示例使用“Fabrikam-user-assigned-identity”。 |
   | **订阅** | 是 | <*Azure-subscription-name*> | 要使用的 Azure 订阅的名称 |
   | **资源组** | 是 | <*Azure-resource-group-name*> | 要使用的资源组的名称。 创建新组或选择现有组。 此示例创建名为“fabrikam-managed-identities-RG”的新组。 |
   | **位置** | 是 | <*Azure-region*> | 用于存储有关资源的信息的 Azure 区域。 此示例使用“美国西部”。 |
   |||||

   现在，你可以将用户分配的标识添加到逻辑应用。 不能将多个用户分配的标识添加到逻辑应用。

1. 在 Azure 门户中的“逻辑应用设计器”中查找并打开逻辑应用。

1. 在逻辑应用菜单的“设置”下选择“标识”，然后选择“用户分配” > “添加”。   

   ![添加用户分配的托管标识](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. 在“添加用户分配的托管标识”窗格的“订阅”列表中，选择你的 Azure 订阅（如果尚未选择）。  从显示该订阅中的所有托管标识的列表中，查找并选择所需的用户分配的标识。 若要筛选列表，请在“用户分配的托管标识”搜索框中输入标识或资源组的名称。 完成后，选择“添加”。

   ![选择要使用的用户分配的标识](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > 如果你收到一个错误，指出你只能有一个托管标识，则表明逻辑应用已与系统分配的标识相关联。 在添加用户分配的标识之前，必须先在逻辑应用上禁用系统分配的标识。

   逻辑应用现已与用户分配的托管标识相关联。

   ![与用户分配的标识关联](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 现在，按照本主题后面的[授予该标识对资源的访问权限](#access-other-resources)的步骤进行操作。

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>在 Azure 资源管理器模板中创建用户分配的标识

若要自动创建和部署 Azure 资源（如逻辑应用），可以使用 [Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，该模板支持[用户分配的标识用于身份验证](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)。 在模板的 `resources` 部分中，逻辑应用的资源定义需要以下各项：

* 一个 `type` 属性设置为 `UserAssigned` 的 `identity` 对象

* 一个子 `userAssignedIdentities` 对象，该对象指定用户分配的资源和名称

此示例演示 HTTP PUT 请求的逻辑应用资源定义，并包含非参数化的 `identity` 对象。 对 PUT 请求和后续 GET 操作的响应还具有此 `identity` 对象：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

如果模板还包括托管标识的资源定义，则可以将 `identity` 对象参数化。 此示例演示子 `userAssignedIdentities` 对象如何引用你在模板的 `variables` 部分中定义的 `userAssignedIdentity` 变量。 此变量引用用户分配的标识的资源 ID。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>授予标识对资源的访问权限

使用逻辑应用的托管标识进行身份验证之前，请在计划使用该标识的 Azure 资源上为该标识设置访问权限。 若要完成此任务，请在目标 Azure 资源上向该标识分配适当的角色。 以下是可使用的选项：

* [Azure 门户](#azure-portal-assign-access)
* [Azure Resource Manager 模板](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) - 有关详细信息，请参阅[使用 Azure RBAC 和 Azure PowerShell 添加角色分配](../role-based-access-control/role-assignments-powershell.md)。
* Azure CLI ([az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) - 有关详细信息，请参阅[使用 Azure RBAC 和 Azure CLI 添加角色分配](../role-based-access-control/role-assignments-cli.md)。
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>在 Azure 门户中分配访问权限

1. 在 [Azure 门户](https://portal.azure.com)中，访问希望托管标识具有访问权限的 Azure 资源。

1. 在资源的菜单中，选择“访问控制 (IAM)” > “角色分配”，你可以在其中查看该资源的当前角色分配。  在工具栏上，选择“添加” > “添加角色分配”。

   ![选择“添加”>“添加角色分配”](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 如果“添加角色分配”选项处于禁用状态，那么你很可能没有权限。 有关可用于管理资源角色的权限的详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

1. 在“添加角色分配”下，选择一个“角色”，该角色授予标识对目标资源的所需访问权限。 

   在本主题的示例中，标识需要一个[可访问 Azure 存储容器中的 Blob 的角色](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)。

   ![选择“存储 Blob 数据参与者”角色](./media/create-managed-service-identity/select-role-for-identity.png)

1. 针对托管标识执行以下步骤：

   * **系统分配的标识**

     1. 在“将访问权限分配到”框中，选择“逻辑应用”。  出现“订阅”属性时，选择与你的标识关联的 Azure 订阅。

        ![为系统分配的标识选择访问权限](./media/create-managed-service-identity/assign-access-system.png)

     1. 在“选择”框下，从列表中选择逻辑应用。 如果列表太长，请使用“选择”框筛选列表。

        ![为系统分配的标识选择逻辑应用](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **用户分配的标识**

     1. 在“将访问权限分配到”框中，选择“用户分配的托管标识”。  出现“订阅”属性时，选择与你的标识关联的 Azure 订阅。

        ![为用户分配的标识选择访问权限](./media/create-managed-service-identity/assign-access-user.png)

     1. 在“选择”框下，从列表中选择标识。 如果列表太长，请使用“选择”框筛选列表。

        ![选择用户分配的标识](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. 完成后，选择“保存”。

   目标资源的角色分配列表现在显示所选的托管标识和角色。 此示例演示如何对一个逻辑应用使用系统分配的标识，以及如何对一组其他逻辑应用使用用户分配的标识。

   ![向目标资源添加了托管标识和角色](./media/create-managed-service-identity/added-roles-for-identities.png)

   有关详细信息，请参阅[通过使用 Azure 门户为托管标识分配对资源的访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。

1. 现在，按照支持托管标识的触发器或操作中[使用标识对访问权限进行身份验证的步骤](#authenticate-access-with-identity)进行操作。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用托管标识对访问权限进行身份验证

[为逻辑应用启用托管标识](#azure-portal-system-logic-app)并[授予该标识对目标资源或实体的访问权限](#access-other-resources)后，可以在[支持托管标识的触发器和操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)中使用该标识。

> [!IMPORTANT]
> 如果你有想要使用系统分配的标识的 Azure 函数，首先[为 Azure 函数](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)启用身份验证。

以下步骤演示了如何通过 Azure 门户将托管标识与触发器或操作一起使用。 若要在触发器或操作的基础 JSON 定义中指定托管标识，请参阅[托管标识身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果尚未这样做，请添加[支持托管标识的触发器或操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

   例如，HTTP 触发器或操作可使用为逻辑应用启用的系统分配的标识。 通常，HTTP 触发器或操作使用这些属性来指定要访问的资源或实体：

   | properties | 必须 | 说明 |
   |----------|----------|-------------|
   | **方法** | 是 | 要运行的操作所使用的 HTTP 方法 |
   | **URI** | 是 | 用于访问目标 Azure 资源或实体的终结点 URL。 URI 语法通常包含 Azure 资源或服务的[资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   | **标头** | 否 | 需要包含在传出请求中的任何标头值，如内容类型 |
   | **查询** | 否 | 需要包含在请求中的任何查询参数，如特定操作的参数或要运行的操作的 API 版本 |
   | **身份验证** | 是 | 用于对目标资源或实体的访问进行身份验证的身份验证类型 |
   ||||

   作为特定示例，假设要在之前为标识设置访问权限的 Azure 存储帐户中的 Blob 上运行[快照 Blob 操作](/rest/api/storageservices/snapshot-blob)。 但是，[Azure Blob 存储连接器](/connectors/azureblob/)当前不提供此操作。 相反，你可以使用 [HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)或另一个 [Blob 服务 REST API 操作](/rest/api/storageservices/operations-on-blobs)来运行此操作。

   > [!IMPORTANT]
   > 若要通过使用 HTTP 请求和托管标识访问防火墙后面的 Azure 存储帐户，请确保你还设置了[允许受信任的 Microsoft 服务访问的例外](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)存储帐户。

   若要运行[快照 Blob 操作](/rest/api/storageservices/snapshot-blob)，HTTP 操作将指定以下属性：

   | properties | 必选 | 示例值 | 说明 |
   |----------|----------|---------------|-------------|
   | **方法** | 是 | `PUT`| 快照 Blob 操作使用的 HTTP 方法 |
   | **URI** | 是 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Azure 全球（公共）环境中使用此语法的 Azure Blob 存储文件的资源 ID |
   | **标头** | 是，适用于 Azure 存储 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure 存储操作所需的 `x-ms-blob-type` 和 `x-ms-version` 标头值。 <p><p>**重要说明**：在 Azure 存储的传出 HTTP 触发器和操作请求中，标头需要 `x-ms-version` 属性以及要运行的操作的 API 版本。 <p>有关详细信息，请参阅以下主题： <p><p>- [请求标头 - 快照 Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Azure 存储服务的版本控制](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **查询** | 是，针对此操作 | `comp` = `snapshot` | 快照 Blob 操作的查询参数名称和值。 |
   |||||

   下面是示例 HTTP 操作，用于显示所有这些属性值：

   ![添加 HTTP 操作以访问 Azure 资源](./media/create-managed-service-identity/http-action-example.png)

1. 现在，向 HTTP 操作添加“身份验证”属性。 在“添加新参数”列表中，选择“身份验证”。 

   ![向 HTTP 操作添加“身份验证”属性](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 并非所有触发器和操作支持都允许你添加身份验证类型。 有关详细信息，请参阅[向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 从“身份验证类型”列表选择“托管标识” 。

   ![对于“身份验证”，请选择“托管标识”](./media/create-managed-service-identity/select-managed-identity.png)

1. 从“托管标识”列表中，根据你的方案从可用选项中进行选择。

   * 如果设置系统分配的标识，请选择“系统分配的托管标识”（如果尚未选择）。

     ![选择“系统分配的托管标识”](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * 如果设置了用户分配的标识，请选择该标识（如果尚未选择）。

     ![选择用户分配的标识](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   此示例将继续处理“系统分配的托管标识”。

1. 在某些触发器和操作上，还会显示“受众”属性，以便设置目标资源 ID。 将“受众”属性设置为[目标资源或服务的资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 否则，默认情况下，“受众”属性使用 `https://management.azure.com/` 资源 ID，该 ID 是 Azure 资源管理器的资源 ID。

   > [!IMPORTANT]
   > 请确保此目标资源 ID 完全匹配 Azure Active Directory (AD) 所需的值，包括任何必需的尾部反斜杠。 例如，所有 Azure Blob 存储帐户的资源 ID 都需要尾部反斜杠。 但是，特定存储帐户的资源 ID 不需要尾部反斜杠。 检查[支持 Azure AD 的 Azure 服务的资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   此示例将“受众”属性设置为 `https://storage.azure.com/`，以便用于身份验证的访问令牌对所有存储帐户都有效。 但是，还可以为特定存储帐户指定根服务 URL `https://fabrikamstorageaccount.blob.core.windows.net`。

   ![将“受众”属性设置为目标资源 ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   有关使用 Azure AD 为 Azure 存储授予访问权限的详细信息，请参阅以下主题：

   * [使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](../storage/common/storage-auth-aad.md)
   * [使用 Azure Active Directory 授予对 Azure 存储的访问权限](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 继续按照所需方式生成逻辑应用。

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>禁用托管标识

若要停止对逻辑应用使用托管标识，可以使用以下选项：

* [Azure 门户](#azure-portal-disable)
* [Azure 资源管理器模板](#template-disable)
* Azure PowerShell
  * [删除角色分配](../role-based-access-control/role-assignments-powershell.md)
  * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [删除角色分配](../role-based-access-control/role-assignments-cli.md)
  * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [删除角色分配](../role-based-access-control/role-assignments-rest.md)
  * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

如果删除逻辑应用，Azure 将从 Azure AD 中自动删除托管标识。

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>在 Azure 门户中禁用托管标识

在 Azure 门户中，首先删除标识对[目标资源](#disable-identity-target-resource)的访问权限。 接下来，关闭系统分配的标识，或从[逻辑应用](#disable-identity-logic-app)删除用户分配的标识。

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>从资源中删除标识访问权限

1. 在 [Azure 门户](https://portal.azure.com)中，转到你要在其中删除托管标识的访问权限的目标 Azure 资源。

1. 从目标资源的菜单中，选择“访问控制 (IAM)”。 在工具栏下，选择“角色分配”。

1. 在“角色”列表中，选择要删除的托管标识。 在工具栏上，选择“删除”。

   > [!TIP]
   > 如果“删除”选项处于禁用状态，那么你很可能没有权限。 有关可用于管理资源角色的权限的详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

托管标识现在已删除，不再具有对目标资源的访问权限。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>在逻辑应用上禁用托管标识

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在“逻辑应用”菜单上的“设置”下选择“标识”，然后按照标识的步骤进行操作： 

   * 选择“系统分配” > “开启” > “保存”。   当 Azure 提示你进行确认时，选择“是”。

     ![禁用系统分配的标识](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * 选择“用户分配”和托管标识，然后选择“删除”。  当 Azure 提示你进行确认时，选择“是”。

     ![删除用户分配的标识](./media/create-managed-service-identity/remove-user-assigned-identity.png)

托管标识现已在逻辑应用上禁用。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中禁用托管标识

如果使用 Azure 资源管理器模板创建了逻辑应用的托管标识，请将 `identity` 对象的 `type` 子属性设置为 `None`。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>后续步骤

* [保护 Azure 逻辑应用中的访问和数据](../logic-apps/logic-apps-securing-a-logic-app.md)
