---
title: 了解 Azure 春季云中的应用和部署
description: Azure 春季云中的应用程序和部署之间的区别。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 81e1925810f374da6f02bf6c3a013b00b5bb9a2c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263891"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>了解 Azure 春季云中的应用和部署

**应用** 和 **部署** 是 Azure 春季云和资源模型中的两个关键概念。 在 Azure 春季云中， *应用* 是一个业务应用或一个微服务的抽象。  部署中运行的*应用程序*版本*之一。*

 ![应用和部署](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Azure 春季云标准层允许一个应用有一个生产部署和一个过渡部署，以便你可以轻松地对其进行蓝色/绿色部署。

## <a name="app"></a>应用
以下功能/属性是在应用级别上定义的。

| 枚举 | 定义 |
|:--|:----------------|
| 公用</br>终结点 | 用于访问应用程序的 URL |
| “自定义”</br>域 | 保护自定义域的 CNAME 记录 |
| 服务</br>绑定 | 在 function.js文件和 *ServiceBusTrigger* 属性中设置的绑定配置属性 |
| 托管</br>标识 | 通过 Azure Active Directory 的管理身份，你的应用可以轻松访问其他 Azure AD 保护的资源，例如 Azure Key Vault |
| 永久</br>存储 | 允许数据在应用重启之外保存的设置 |

## <a name="deployment"></a>部署

以下功能/属性在部署级别上定义，并将在交换生产/过渡部署时进行交换。

| 枚举 | 定义 |
|:--|:----------------|
| CPU | 每个应用实例的 vcore 数 |
| 内存 | 用于分配内存以纵向扩展或横向扩展部署的设置 |
| 实例</br>计数 | 手动或自动设置的应用实例数 |
| 自动缩放 | 根据预定义的规则和计划自动缩放实例计数 |
| JVM</br>选项 | 设置： JAVA_OPTS |
| 环境</br>变量 | 适用于整个 Azure 春季云环境的设置 |
| 运行时</br>版本 | Java 8/Java 11|

## <a name="restrictions"></a>限制

* **应用必须有一个生产部署**：删除生产部署会被 API 阻止。 删除之前，应将它交换到过渡环境。
* **一个应用最多可以有两个部署**：创建两个以上的部署会被 API 阻止。 将新的二进制文件部署到现有的生产或过渡部署。
* **"基本" 层中不提供部署管理**：使用标准层进行蓝绿色部署功能。

## <a name="see-also"></a>另请参阅
* [在 Azure 春季云中设置过渡环境](spring-cloud-howto-staging-environment.md)