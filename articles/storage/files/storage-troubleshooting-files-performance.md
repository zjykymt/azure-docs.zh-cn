---
title: Azure 文件存储性能故障排除指南
description: 排查 Azure 文件共享已知的性能问题。 遇到这些问题时，发现可能的原因和相关解决方法。
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: fe1460d4353addff1b8e3095cfe06c1fcb3b7bd0
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782364"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>排查 Azure 文件存储性能问题

本文列出了与 Azure 文件共享相关的一些常见问题。 其中提供了这些问题的潜在原因和解决方法。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延迟、低吞吐量和一般性能问题

### <a name="cause-1-share-was-throttled"></a>原因1：共享已中止

当达到文件共享的 IOPS、入口或出口限制时，将会限制请求。 若要了解标准文件共享和高级文件共享的限制，请参阅 [文件共享和文件缩放目标](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets)。

若要确认共享是否受到限制，可以利用门户中的“Azure 指标”。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务”，然后搜索“指标”********。

1. 选择“指标”。

1. 选择你的存储帐户作为资源。

1. 选择“文件”作为指标命名空间。****

1. 选择“事务”作为指标。****

1. 添加 **ResponseType** 的筛选器，并检查是否有任何请求的响应代码为 **SuccessWithThrottling**（适用于 SMB）或 **ClientThrottlingError**（适用于 REST）。

![高级文件共享的指标选项](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 若要在文件共享受到限制时接收警报，请参阅 [如何在文件共享受到限制](#how-to-create-an-alert-if-a-file-share-is-throttled)的情况下创建警报。

### <a name="solution"></a>解决方案

- 如果使用的是标准文件共享，请在存储帐户上启用 [大型文件](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) 共享。 大型文件共享最多支持每个共享 10000 IOPS。
- 如果你使用的是高级文件共享，则增加预配的文件共享大小以增加 IOPS 限制。 若要了解详细信息，请参阅 Azure 文件规划指南中的 [了解高级文件共享的设置](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) 部分。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因2：元数据/命名空间繁重的工作负荷

如果大多数请求以元数据为中心（例如 createfile/openfile/closefile/queryinfo/querydirectory），则与读/写操作相比，延迟将会更严重。

若要确认大多数请求是否以元数据为中心，可以使用上述相同的步骤。 不要添加 **ResponseType** 的筛选器，而是添加 **API 名称**的筛选器。

![筛选指标中的 API 名称](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>解决方法

- 检查是否可以修改应用程序来减少元数据操作的数量。
- 在文件共享上添加 VHD，并从客户端通过 SMB 装载 VHD，以便对数据执行文件操作。 此方法适用于单个写入器和多个读取器方案，并允许元数据操作在本地进行，提供与本地直连存储类似的性能。

### <a name="cause-3-single-threaded-application"></a>原因3：单线程应用程序

如果客户使用的应用程序是单线程的，这可能会导致 IOPS/吞吐量明显低于最大可能的值，具体取决于预配的共享大小。

### <a name="solution"></a>解决方案

- 通过增加线程数来提高应用程序的并行度。
- 切换到支持并行度的应用程序。 例如，对于复制操作，客户可以在 Windows 客户端中使用 AzCopy 或 RoboCopy，或者在 Linux 客户端中使用 **parallel** 命令。

## <a name="very-high-latency-for-requests"></a>请求的延迟很高

### <a name="cause"></a>原因

客户端 VM 所在的区域可能与文件共享所在的区域不同。

### <a name="solution"></a>解决方案

- 从与文件共享位于同一区域的 VM 运行应用程序。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>客户端无法实现网络支持的最大吞吐量

此问题的潜在原因之一是缺少 SMB 多通道支持。 目前，Azure 文件共享仅支持单个通道，因此只会建立从客户端 VM 到服务器的一个连接。 此单一连接限定为客户端 VM 上的单一核心，因此，可从 VM 实现的最大吞吐量受限于单个核心。

### <a name="workaround"></a>解决方法

- 获取核心更大的 VM 可能有助于提高吞吐量。
- 从多个 VM 运行客户端应用程序会提高吞吐量。

- 尽可能地使用 REST API。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>与 Windows 客户端相比，Linux 客户端上的吞吐量要低得多。

### <a name="cause"></a>原因

这是 Linux 上实施的 SMB 客户端的一个已知问题。

### <a name="workaround"></a>解决方法

- 跨多个 VM 分散负载。
- 在同一 VM 上，通过 **nosharesock** 选项使用多个装入点，并将负载分散到这些装入点。
- 在 Linux 上，尝试装载 **nostrictsync** 选项，以避免在每个 **fsync** 调用上强制进行 SMB 刷新。 对于 Azure 文件，此选项不会影响数据一致性，但可能会导致目录列表中的文件元数据陈旧 (**ls-l** 命令) 。 直接查询文件的元数据（**stat** 命令）会返回最新的文件元数据。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>涉及大量打开/关闭操作的元数据密集型工作负荷出现较高的延迟。

### <a name="cause"></a>原因

缺少目录租约支持。

### <a name="workaround"></a>解决方法

- 如果可能，请避免短时间内在同一目录中使用过多的打开/关闭句柄。
- 对于 Linux Vm，请通过指定**actimeo = \<sec> **作为装载选项来增加目录条目缓存超时。 默认情况下，该超时为 1 秒，使用更大的值（例如 3 或 5）可能有所帮助。
- 对于 Linux VM，请将内核升级到 4.20 或更高版本。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL 上的 IOPS 较低

### <a name="cause"></a>原因

CentOS/RHEL 不支持大于 1 的 IO 深度。

### <a name="workaround"></a>解决方法

- 升级到 CentOS 8/RHEL 8。
- 改用 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>在 Linux 中将文件复制到 Azure 文件以及从中复制文件时速度缓慢

如果在向/从 Azure 文件复制文件时速度缓慢，请查看 Linux 故障排除指南中的[在 Linux 中向/从 Azure 文件复制文件时速度缓慢](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)部分。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS 出现抖动/锯齿模式

### <a name="cause"></a>原因

客户端应用程序总是超过基线 IOPS。 目前，请求负载没有服务端平滑处理，因此，如果客户端超过基线 IOPS，则服务会对其进行限制。 该限制可能导致客户端遇到抖动/锯齿 IOPS 模式。 在这种情况下，客户端实现的平均 IOPS 可能低于基线 IOPS。

### <a name="workaround"></a>解决方法

- 减少客户端应用程序的请求负载，使共享不会受到限制。
- 提高共享配额，使共享不会受到限制。

## <a name="excessive-directoryopendirectoryclose-calls"></a>过多的 DirectoryOpen/DirectoryClose 调用

### <a name="cause"></a>原因

如果最频繁的 API 调用中包括 DirectoryOpen/DirectoryClose 调用，而你预计客户端不会发出这么多的调用，则问题可能与 Azure 客户端 VM 上安装的防病毒软件有关。

### <a name="workaround"></a>解决方法

- [Windows 四月平台更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)中提供了此问题的修复措施。

## <a name="file-creation-is-slower-than-expected"></a>文件创建速度慢于预期

### <a name="cause"></a>原因

依赖于创建大量文件的工作负荷在高级文件共享和标准文件共享中的性能没有明显差异。

### <a name="workaround"></a>解决方法

- 无。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>在 Windows 8.1 或 Server 2012 R2 中的性能不佳

### <a name="cause"></a>原因

对于 IO 密集型工作负荷，访问 Azure 文件时的延迟要高于预期。

### <a name="workaround"></a>解决方法

- 安装可用的[修补程序](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如果文件共享受到限制，如何创建警报

1. 在**Azure 门户**中转到你的**存储帐户**。
2. 在 "监视" 部分中，单击 " **警报** "，然后单击 " **+ 新建警报规则**"。
3. 单击 " **编辑资源**"，选择存储帐户的 **文件资源类型** ，然后单击 " **完成**"。 例如，如果存储帐户名称为 "contoso"，则选择 "contoso/文件" 资源。
4. 单击 " **选择条件** " 添加条件。
5. 你会看到存储帐户支持的信号列表，请选择 " **事务** " 指标。
6. 在 " **配置信号逻辑** " 边栏选项卡上，单击 " **维度名称** " 下拉箭头，然后选择 " **响应类型**"。
7. 单击 " **维度值** " 下拉箭头，然后选择 "适用于 SMB) 的 **SuccessWithThrottling** (" 或 " **ClientThrottlingError** (为 REST) "。

  > [!NOTE]
  > 如果 "SuccessWithThrottling" 或 "ClientThrottlingError" 维度值未列出，则表示资源尚未被限制。 若要添加维度值，请单击 "**维度值**" 下拉箭头旁边的 "**添加自定义值**"，键入 " **SuccessWithThrottling** " 或 " **ClientThrottlingError**"，然后单击 **"确定"** ，然后重复步骤 #7。

8. 单击 " **维度名称** " 下拉箭头，然后选择 " **文件共享**"。
9. 单击 " **维度值** " 下拉箭头，然后选择要对其发出警报 () 的文件共享。

  > [!NOTE]
  > 如果文件共享是标准文件共享，请选择 " **所有当前和未来值**"。 "维度值" 下拉列表将不会列出) 的文件 (共享，因为 "每个共享" 度量值不可用于标准文件共享。 如果存储帐户中的任何文件共享受到限制，并且警报不会确定限制了哪个文件共享，则会触发标准文件共享的限制警报。 由于每个共享度量值不可用于标准文件共享，因此建议每个存储帐户都有一个文件共享。

10. 定义 **警报参数** (阈值、运算符、聚合粒度和计算频率) 并单击 " **完成**"。

  > [!TIP]
  > 如果使用的是静态阈值，则如果当前正在限制文件共享，则度量值图表可帮助确定合理的阈值。 如果使用的是动态阈值，则度量值图表将根据最近的数据显示计算的阈值。

11. 单击 " **选择操作组** "，通过选择现有操作组或创建新的操作组，将 **操作组** (电子邮件、SMS 等 ) 添加到警报。
12. 填写警报 **详细信息** ，如 **警报规则名称**、 **描述** 和 **严重性**。
13. 单击 " **创建警报规则** " 以创建警报。

若要了解有关在 Azure Monitor 中配置警报的详细信息，请参阅 [Microsoft Azure 中的警报概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="see-also"></a>另请参阅
* [在 Windows 中排查 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
* [在 Linux 中排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)
* [有关 Azure 文件的常见问题解答 (FAQ)](storage-files-faq.md)
