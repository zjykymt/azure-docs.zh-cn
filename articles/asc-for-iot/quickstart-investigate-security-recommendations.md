---
title: 快速入门：调查安全建议
description: 使用适用于 IoT 的 Azure 安全中心安全服务调查安全建议。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: mlottner
ms.openlocfilehash: aa241a9108be32f88357065732c6f283d312be6c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143991"
---
# <a name="quickstart-investigate-security-recommendations"></a>快速入门：调查安全建议


适用于 IoT 的 Azure 安全中心对建议的及时分析和缓解是改进整个 IoT 解决方案中的安全状况和减少攻击面的最佳方式。

本快速入门将介绍每个 IoT 安全建议中可用的信息，并说明如何深化和使用每个建议和相关设备的详细信息来减少风险。

让我们开始吧。

## <a name="investigate-new-recommendations"></a>调查新的建议

IoT 中心建议列表显示 IoT 中心的所有聚合安全建议。

1.  在 Azure 门户中，打开要调查是否存在新建议的“IoT 中心”  **** 。

1.  从“安全” 菜单，选择“建议” ****  **** 。 所有 IoT 中心的安全建议都将显示，带有“新”标志的建议标记你过去 24 小时的建议 ****  。 

    [ ![使用适用于 IoT 的 ASC 调查安全建议](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  从列表中选择并打开任何建议，可打开建议详细信息并深化了解细节。

## <a name="security-recommendation-details"></a>安全建议详细信息

打开每个聚合建议以显示触发建议的每个设备的详细建议说明、修正步骤、设备 ID。 它还显示建议的严重性和使用 Log Analytics 的直接调查访问。

1.  从“IoT 中心” \>“安全”和 \>“建议” 列表中选择打开任何安全建议 ****  ****  **** 。

1.  查看建议“说明”、“严重性”和在聚合期间发出此建议的所有设备的“设备详细信息” ****  ****  。 

1.  查看建议细节后，使用“手动修正步骤” 说明可帮助修正并解决导致建议的问题 **** 。 

    [ ![使用适用于 IoT 的 ASC 修正安全建议](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  通过在深入了解页面中选择所需的设备，浏览特定设备的建议详细信息。

    [ ![使用适用于 IoT 的 ASC 调查设备的特定安全建议](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  如果需要进一步调查，请使用此链接“在 Log Analytics 中调查建议”  **** 。 


## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何创建自定义警报…

> [!div class="nextstepaction"]
> [创建自定义警报](quickstart-create-custom-alerts.md)
