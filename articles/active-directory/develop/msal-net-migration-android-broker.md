---
title: 使用代理将 Xamarin Android 应用迁移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 了解如何将使用 Microsoft Authenticator 或 Intune 公司门户的 Xamarin Android 应用迁移到 MSAL.NET。
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47902b29da1dfe20fb54d633a0559e6a337fd771
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89183559"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>将使用 broker 的 Android 应用程序从 ADAL.NET 迁移到 MSAL.NET

如果你当前使用的是适用于 .NET (ADAL.NET) 和 [身份验证代理](brokered-auth.md)的 Azure Active Directory authentication 库的 Xamarin Android 应用程序，则可以迁移到 [适用于 .Net 的 Microsoft 身份验证库 ](msal-overview.md) (MSAL.NET) 。

## <a name="prerequisites"></a>必备条件

* Xamarin Android 应用已与需要迁移到 MSAL.NET 的代理 ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [Intune 公司门户](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) 和 ADAL.NET 集成。

## <a name="step-1-enable-the-broker"></a>步骤 1：启用中介

<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
在 ADAL.NET 中，将基于每个身份验证上下文启用 broker 支持。

若要调用 broker，必须 `useBroker` 在构造函数中将*true*设置为 true `PlatformParameters` ：

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

在适用于 Android 的特定于平台的页面呈现器代码中，将 `useBroker` 标志设置为 true：

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

然后，在获取令牌调用中包含参数：

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
在 MSAL.NET 中，中介支持是按 PublicClientApplication 启用的。

使用 `WithBroker()` 参数 (默认设置为 true) 以调用 broker：

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

然后，在 AcquireToken 调用中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>步骤2：设置活动

在 ADAL.NET 中，传递的活动 (通常是) MainActivity 的一部分，如 [步骤1：启用 broker](#step-1-enable-the-broker)中所示。

MSAL.NET 也使用活动，但没有代理的常规 Android 使用不需要此操作。 若要使用 broker，请将活动设置为发送和接收来自 broker 的响应。

<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
活动会传递到 Android 特定平台中的 PlatformParameters。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

在 MSAL.NET 中，请执行以下两项操作来设置 Android 的活动：

1. 在中 `MainActivity.cs` ，将设置 `App.RootViewController`  为， `MainActivity` 以确保有活动正在调用 broker。

    如果未正确设置，则可能会收到此错误： `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. 在 AcquireTokenInteractive 调用中，使用 `.WithParentActivityOrWindow(App.RootViewController)` 并传入对要使用的活动的引用。 此示例将使用 MainActivity。

例如：

在 *App.cs*中：

```CSharp
   public static object RootViewController { get; set; }
```

在 *MainActivity.cs*中：

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

在 AcquireToken 调用中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>后续步骤

有关将 MSAL.NET 与 Xamarin 配合使用时特定于 Android 的注意事项的详细信息，请参阅使用 [MSAL.NET 的 Xamarin Android 的配置要求和故障排除提示](msal-net-xamarin-android-considerations.md)。