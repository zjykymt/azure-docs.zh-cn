---
title: 从 Android 连接到 Windows 虚拟桌面-Azure
description: 如何使用 Android 客户端连接到 Windows 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ebf91f68e45148d9a609ff671ffa4683bd74c82c
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226108"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>通过 Android 客户端连接到 Windows 虚拟桌面

> 适用于： Android 4.1 及更高版本、Chromebook 和 ChromeOS 53 及更高版本。

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/connect-android-2019.md)。

可以使用可下载的客户端从 Android 设备访问 Windows 虚拟桌面资源。 你还可以在支持 Google Play 商店的 Chromebook 设备上使用 Android 客户端。 本指南将说明如何设置 Android 客户端。

## <a name="install-the-android-client"></a>安装 Android 客户端

若要开始，请在 Android 设备上 [下载](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) 并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供的源，获取可在 Android 设备上访问的托管资源的列表。

若要订阅源，请执行以下操作：

1. 在“连接中心”点击“+”，然后点击“远程资源源”。
2. 在 " **源 url** " 字段中输入源 url。 源 URL 可以是 URL，也可以是电子邮件地址。
   - 如果使用 URL，请使用管理员为你提供的（通常为） <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> 。
   - 若要使用电子邮件地址，请输入你的电子邮件地址。 如果管理员配置了服务器，则客户端将搜索与你的电子邮件地址关联的 URL。
   - 若要通过 US Gov 门户进行连接，请使用 <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> 。
3. 点击“下一步”。
4. 在系统提示时提供凭据。
   - 对于 " **用户名**"，为用户名提供访问资源的权限。
   - 对于 " **密码**"，请提供与用户名关联的密码。
   - 如果管理员以这种方式配置了身份验证，则还可能会提示你提供其他因素。

订阅后，连接中心应显示远程资源。

订阅源后，源的内容会定期自动更新。 资源可能会根据管理员所做的更改而进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 Android 客户端的详细信息，请查看 [android 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)。
