---
title: Azure 门户：部署适用于 Azure 的开源 FHIR 服务器 - Azure API for FHIR
description: 本快速入门介绍了如何使用 Azure 门户部署 Microsoft 开源 FHIR 服务器。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 7fa119db0c974c93aff667060d153b21b8de16bb
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843481"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>快速入门：使用 Azure 门户部署开源 FHIR 服务器

本快速入门介绍如何使用 Azure 门户在 Azure 中部署开源 FHIR 服务器。 我们将使用[开源存储库](https://github.com/Microsoft/fhir-server)中的简单部署链接

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="github-open-source-repository"></a>GitHub 开源存储库

导航到 [GitHub 部署页面](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md)并找到“部署到 Azure”按钮：

![开源部署页面](media/quickstart-oss-portal/deployment-page-oss.png)

单击“部署”按钮，此时会打开 Azure 门户。

## <a name="fill-in-deployment-parameters"></a>填写部署参数

选择创建一个新资源组，并为其指定名称。 服务的名称只能是另一个必需参数。

![自定义部署参数](media/quickstart-oss-portal/deployment-custom-parameters.png)

请注意，部署将直接从 GitHub 上的开源存储库中提取源代码。 如果已对存储库创建分支，则可指向自己的分支和特定分支。

填写详细信息后，可开始部署。

## <a name="validate-fhir-server-is-running"></a>验证 FHIR 服务器是否正在运行

部署完成后，可将浏览器指向 `https://SERVICENAME.azurewebsites.net/metadata` 来获取功能语句。 服务器首次响应需要 1 分钟或更长时间。

## <a name="clean-up-resources"></a>清理资源

当不再需要时，可删除资源组及所有相关资源。 为此，请选择包含预配资源的资源组，接着选择“删除资源组”，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

在本教程中，你已将适用于 Azure 的 Microsoft 开源 FHIR 服务器部署到订阅中。 若要了解如何使用 Postman 访问 FHIR API，请转到 Postman 教程。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR API](access-fhir-postman-tutorial.md)