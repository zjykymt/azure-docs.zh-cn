---
title: 授权数据操作
titleSuffix: Azure Storage
description: 了解授权访问 Azure 存储的各种方式，包括 Azure Active Directory、共享密钥授权或共享访问签名 (SAS)。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a56fdf8bc3bfa93c12cce66664e68b223fa97ff2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029529"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>授权访问 Azure 存储中的数据

每次访问存储帐户中的数据时，客户端都会通过 HTTP/HTTPS 向 Azure 存储发出请求。 每个对安全资源的请求都必须经过授权，以便服务确保客户端具有访问数据所需的权限。

下表介绍了多个选项，Azure 存储提供这些选项，用于授权对资源的访问：

| Azure 项目 | 共享密钥（存储帐户密钥） | 共享访问签名 (SAS) | Azure Active Directory (Azure AD) | 本地 Active Directory 域服务（预览） | 匿名公共读取访问权限 |
| -------------- | -------------------------------- | ----------------------------- | --------------------------------- | ------------------------------------------------------ | ---------------------------- |
|Azure Blob     |[支持](/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |[支持](storage-auth-aad.md)         |不支持|[支持](../blobs/storage-manage-access-to-resources.md)         |
|Azure 文件存储 (SMB)     |[支持](/rest/api/storageservices/authorize-with-shared-key/)         |不支持         |[支持，仅适用于 AAD 域服务](../files/storage-files-active-directory-overview.md)         |[支持，凭据必须同步到 Azure AD](../files/storage-files-active-directory-overview.md)|不支持         |
|Azure 文件存储 (REST)     |[支持](/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |不支持         |不支持 |不支持         |
|Azure 队列     |[支持](/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |[支持](storage-auth-aad.md)         |不受支持 | 不支持         |
|Azure 表     |[支持](/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |不支持         |不支持| 不支持         |

下面简要介绍每个授权选项：

- 用于 Blob 和队列的 **Azure Active Directory (Azure AD) 集成**。 Azure AD 提供了基于角色的访问控制（RBAC）来控制客户端对存储帐户中资源的访问。 有关用于 Blob 和队列的 Azure AD 集成的详细信息，请参阅[使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](storage-auth-aad.md)。

- Azure Active Directory Azure 文件的**域服务（AZURE AD DS）身份验证**。 Azure 文件通过 Azure AD DS 支持通过服务器消息块（SMB）进行基于标识的授权。 可以使用 RBAC 对客户端对存储帐户中的 Azure 文件资源的访问权限进行精细控制。 有关使用域服务进行 Azure 文件身份验证的详细信息，请参阅[概述](../files/storage-files-active-directory-overview.md)。

- Azure 文件的**本地 Active Directory 域服务（AD DS 或本地 AD DS）身份验证（预览版）** 。 Azure 文件通过 AD DS 支持通过 SMB 进行基于标识的授权。 你的 AD DS 环境可以托管在本地计算机中，也可以托管在 Azure Vm 中。 使用从本地或 Azure 中的已加入域的计算机 AD DS 凭据支持 SMB 访问文件。 可以结合使用 RBAC 与共享级别访问控制和 NTFS Dacl 的组合来执行目录/文件级权限。 有关使用域服务进行 Azure 文件身份验证的详细信息，请参阅[概述](../files/storage-files-active-directory-overview.md)。

- 用于 blob、文件、队列和表的**共享密钥授权**。 使用共享密钥的客户端会随使用存储帐户访问密钥签名的每个请求传递一个标头。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key/)。
- 用于 blob、文件、队列和表的**共享访问签名**。 共享访问签名 (SAS) 针对存储帐户中的资源提供有限的委托访问权限。 通过对签名的有效时间间隔或对它授予的权限添加约束，可灵活地管理访问权限。 有关详细信息，请参阅[使用共享访问签名（SAS）](storage-sas-overview.md)。
- 用于容器和 blob 的**匿名公共读取访问**。 无需授权。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/storage-manage-access-to-resources.md)。  

默认情况下，Azure 存储中的所有资源都受到保护，并且只能由帐户所有者使用。 虽然你可以使用上述任何授权策略为客户端授予访问存储帐户资源的权限，但 Microsoft 建议尽可能使用 Azure AD，以获得最大的安全性和易用性。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](storage-auth-aad.md)
- [通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key/)
- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)
