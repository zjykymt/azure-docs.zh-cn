---
title: 应用服务环境的可用性区域支持
description: 了解如何部署应用服务环境，使你的应用实现区域冗余。
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15b129db56a9c6854bc3c1f2814a8776ec39adc6
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961561"
---
# <a name="availability-zone-support-for-app-service-environments"></a>应用服务环境的可用性区域支持

可以将应用服务环境 (ASE) 部署到可用性区域 (AZ) 中。  客户可将内部负载均衡器 (ILB) ASE 部署到 Azure 区域中的特定 AZ。 如果你将 ILB ASE 固定到特定 AZ，则 ILB ASE 使用的资源会固定到指定的 AZ，或以区域冗余方式进行部署。  

显式部署到 AZ 的 ILB ASE 被视为一个区域性资源，因为 ILB ASE 固定到特定区域。 以下 ILB ASE 依赖项将固定到指定区域：

- ASE 的内部负载均衡器 IP 地址
- ASE 用来管理和运行 Web 应用程序的计算资源

在区域性 ILB ASE 上部署的 Web 应用程序的远程文件存储使用区域冗余存储 (ZRS)。

除非按本文中所述的步骤操作，否则不会以区域性方式自动部署 ILB ASE。 不能将具有公共 IP 地址的外部 ASE 固定到特定的可用性区域。 

可以在以下任何区域中创建区域性 ILB ASE：

- 美国中部
- 美国东部
- 美国东部 2
- 美国东部 2 (EUAP) 
- 法国中部 
- 日本东部
- 北欧
- 西欧
- 东南亚
- 英国南部
- 美国西部 2

即使同一区域中的其他局部区域发生服务中断，在区域性 ILB ASE 上部署的应用程序也会继续运行并为该 ASE 上的流量提供服务。  非运行时行为（包括应用程序服务计划缩放、应用程序创建、应用程序配置和应用程序发布）可能仍然会受其他可用性区域中的中断的影响。 区域性 ILB ASE 的区域固定部署仅确保已部署应用程序的持续正常运行。

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>如何在可用性区域中部署应用服务环境 ##

必须使用 ARM 模板创建区域性 ILB ASE。 通过 ARM 模板创建区域性 ILB ASE 后，可以通过 Azure 门户和 CLI 查看它以及与它进行交互。  仅在初次创建区域性 ILB ASE 时才需要使用 ARM 模板。

在 ARM 模板中指定区域性 ILB ASE 时需要进行的唯一更改是新的 zones 属性。 zones 属性应当设置为值“1”、“2”或“3”，具体取决于 ILB ASE 应当固定到的逻辑可用性区域。

下面的示例 ARM 模板代码片段显示了新的 zones 属性，该属性指定应将 ILB ASE 固定到区域 2。

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

若要使你的应用实现区域冗余，需要部署两个区域性 ILB ASE。 这两个区域性 ILB ASE 必须位于不同的可用性区域中。 然后，你需要将应用部署到每个 ILB ASE 中。 创建应用后，你需要配置负载均衡解决方案。 建议的解决方案是在区域性 ILB ASE 的上游部署[区域冗余应用程序网关](../../application-gateway/application-gateway-autoscaling-zone-redundant.md)。 

## <a name="in-region-data-residency"></a>区域中数据驻留 ##

在可用性区域中部署的 ILB ASE 仅存储部署了区域性 ILB ASE 的区域中的客户数据。 网站文件内容以及客户提供的设置和存储在应用服务中的机密将保留在部署了区域性 ILB ASE 的区域内。

客户可执行此前在“如何在可用性区域中部署应用服务环境”部分所述的步骤，确保单一的区域数据驻留。 根据这些步骤配置应用服务环境后，在可用性区域中部署的应用服务环境将满足区域数据驻留要求，包括在 [Azure 信任中心](https://azuredatacentermap.azurewebsites.net/)指定的那些要求。

客户可以通过执行以下步骤来验证是否已正确配置应用服务环境将数据存储在单个区域中： 

1. 使用 [资源浏览器](https://resources.azure.com)，导航到应用服务环境的 ARM 资源。  Ase */hostingEnvironments*下列出。
2. 如果 ARM JSON 语法的视图中存在 *区域* 属性，并且该属性包含一个值为 "1"、"2" 或 "3" 的单值 JSON 数组，则 ASE 将 zonally 部署，客户数据将保留在同一区域中。
2. 如果 " *区域* " 属性不存在，或者属性没有前面指定的有效区域值，则不会对 ASE 进行 zonally 部署，并且不会以独占方式将客户数据存储在同一区域中。