---
title: Azure Cosmos DB 查询语言中的 EXP
description: 了解 Azure Cosmos DB 中的 Exponent (EXP) SQL 系统函数，该函数返回指定数值表达式的指数值
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74873312"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 返回指定数值表达式的指数值。  
  
## <a name="syntax"></a>语法
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="remarks"></a>备注
  
  常量 e (2.718281…) 是自然对数的底数**e**。  
  
  数字的指数是常量 e 使用该数字进行幂运算  。 例如 EXP(1.0) = e^1.0 = 2.71828182845905，EXP(10) = e^10 = 22026.4657948067。  
  
  某个数的自然对数的指数就是该数本身：EXP (LOG (n)) = n。 并且某个数的指数的自然对数也是该数本身：LOG (EXP (n)) = n。  
  
## <a name="examples"></a>示例
  
  以下示例声明一个变量并返回到指定变量 (10) 的指数值。  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 下面是结果集：  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 以下示例将返回 20 的自然对数的指数值以及 20 的指数的自然对数。 由于这些函数互为反函数，因此在这两种情况下，用于浮点数学舍入的返回值均为 20。  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 下面是结果集：  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
