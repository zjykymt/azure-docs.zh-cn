---
title: 映射数据流窗口转换
description: Azure 数据工厂映射数据流窗口转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606229"
---
# <a name="azure-data-factory-window-transformation"></a>Azure 数据工厂窗口转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在窗口转换中，将定义数据流中基于窗口的列的聚合。 在表达式生成器中，可以定义基于数据或时间窗口（SQL OVER 子句）的不同类型的聚合，如 LEAD、LAG、NTILE、CUMEDIST、RANK 等。 输出中将生成包含这些聚合的新字段。 还可以包含可选的分组字段。

![窗口选项](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Over
为窗口转换设置列数据的分区。 SQL 等效项是 SQL 中 Over 子句中的 ```Partition By```。 如果希望创建用于分区的计算或表达式，可以将鼠标悬停在列名上并选择“计算列”。

![窗口选项](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>排序
Over 子句的另一部分是设置 ```Order By```。 该操作将设置数据排序顺序。 还可以在此列字段中为计算值创建表达式以进行排序。

![窗口选项](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>范围
接下来，将窗口框架设置为“无界”或“有界”。 若要设置无界窗框，请将滑块两端设置为“无界”。 如果在无界和当前行之间选择一个设置，那么必须设置偏移量的开始值和结束值。 这两个值都是正整数。 可以使用数据中的相对数字或值。

窗口滑块有两个要设置的值：当前行之前的值和当前行之后的值。 “开始”和“结束”偏移与滑块上的两个选择器相匹配。

![窗口选项](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>窗口列
最后，使用表达式生成器定义你希望与数据窗口一起使用的聚合，例如 RANK、COUNT、MIN、MAX、DENSE RANK、LEAD 和 LAG 等。

![窗口选项](media/data-flow/windows7.png "windows 7")

此处列出了可通过表达式生成器采用 ADF 数据流表达式语言使用的聚合和分析函数的完整列表：https://aka.ms/dataflowexpressions。

## <a name="next-steps"></a>后续步骤

如果正在查找简单的分组依据聚合，则使用[聚合转换](data-flow-aggregate.md)
