---
title: 内存优化的 Dv2 和 DSv2 系列 VM - Azure 虚拟机
description: Dv2 和 DSv2 系列 VM 的规范。
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: aee88420fe8b88de9af9a2397316dd065144e610
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685283"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>内存优化的 Dv2 和 Dsv2 系列

Dv2 和 Dsv2 系列是原 D 系列的后续系列，其特点是 CPU 功能更强大。 DSv2 系列大小在 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake) 或 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行。 Dv2 系列的内存和磁盘配置与 D 系列相同。

## <a name="dv2-series-11-15"></a>Dv2 系列 11-15

Dv2 系列大小在 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake) 或 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行。

ACU：210 - 250

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大数据磁盘数/吞吐量：IOPS | 最大 NIC 数|预期网络带宽 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2|1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> 实例对于专用于单个客户的硬件独立。
<sup>2</sup> 25000 Mbps，具有加速网络。

## <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

DSv2 系列大小在 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake) 或 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行。

ACU：210 - 250 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期网络带宽 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> DSv2 系列 VM 可能的最大磁盘吞吐量（IOPS 或 Mbps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[为实现高性能而设计](./premium-storage-performance.md)。
<sup>2</sup>  实例隔离于基于 Intel Haswell 的硬件并专用于单个客户。  
<sup>3</sup> 受约束的可用核心大小。  
<sup>4</sup> 25000 Mbps，具有加速网络。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器： [定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息： [磁盘类型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。