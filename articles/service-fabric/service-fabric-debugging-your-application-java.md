---
title: 在 Eclipse 中调试应用程序
description: 通过在本地开发群集上采用 Eclipse 进行开发和调试，来提高服务的可靠性和性能。
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-java
ms.author: suhuruli
ms.openlocfilehash: 32472c6caafe62d629ee3a5ffb8c758624228efd
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87316607"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>使用 Eclipse 调试 Java Service Fabric 应用程序
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. 按照[设置 Service Fabric 开发环境](service-fabric-get-started-linux.md)中的步骤启动本地开发群集。

2. 更新要调试的服务的 entryPoint.sh，以便使用远程调试参数启动 java 进程。 可以在以下位置找到此文件：`ApplicationName\ServiceNamePkg\Code\entrypoint.sh`。 在此示例中调试时，设置端口 8001。

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. 通过将要调试的服务的实例计数或副本计数设置为 1 来更新应用程序清单。 此设置可避免用于调试的端口出现冲突。 例如，对于无状态服务，设置 `InstanceCount="1"`；对于有状态服务，将目标和最小副本集大小设置为 1，如下所示：`TargetReplicaSetSize="1" MinReplicaSetSize="1"`。

4. 部署应用程序。

5. 在 Eclipse IDE 中，选择“运行”->“调试配置”->“远程 Java 应用程序和输入连接属性”  ，并按如下所示设置属性：

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  在所需的位置设置断点并调试应用程序。

如果应用程序崩溃，可能还想要启用 coredumps。 在 shell 中执行 `ulimit -c`，如果它返回 0，则未启用 coredumps。 若要启用不受限制的 coredumps，请执行以下命令：`ulimit -c unlimited`。 还可以使用命令 `ulimit -a` 验证状态。  如果要更新 coredump 生成路径，请执行 `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`。 

### <a name="next-steps"></a>后续步骤

* [使用 Linux Azure 诊断收集日志](./service-fabric-diagnostics-event-aggregation-lad.md)。
* [在本地监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)。
