---
title: Microsoft 标识平台上的 OAuth 2.0 和 OpenID 连接协议 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft 标识平台终结点支持的 OAuth 2.0 和 OpenID Connect 协议指南。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 12edbcda7354d9d6d4b03ebe32304d988b2eb579
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751458"
---
# <a name="oauth-20-and-openid-connect-protocols-on-microsoft-identity-platform"></a>Microsoft 标识平台上的 OAuth 2.0 和 OpenID 连接协议

标识为服务的 Microsoft 标识平台终结点使用行业标准协议 OpenID Connect (OIDC) 和 OAuth 2.0 实现身份验证和授权。 尽管此服务与标准兼容，但这些协议的两个实现之间仍然存在微妙的差异。 如果选择通过直接发送和处理 HTTP 请求，或使用第三方开放源代码库来编写代码，而不是使用我们的其中一个[开放源代码库](reference-v2-libraries.md)，则可以参考此处提供的有用信息。

## <a name="the-basics"></a>基础知识

几乎在所有的 OAuth 2.0 和 OpenID Connect 流中，都有四个参与交换的对象：

![显示 OAuth 2.0 角色的关系图](./media/active-directory-v2-flows/protocols-roles.svg)

* “授权服务器”是 Microsoft 标识平台终结点，它负责确保用户的标识、授予和吊销对资源的访问权限，以及颁发令牌。 授权服务器也称为标识提供程序，它安全地处理与用户信息、其访问以及流中各方之间的信任关系有关的任何内容。
* 资源所有者通常是最终用户。 它是拥有数据的一方，具有允许客户端访问该数据或资源的能力。
* OAuth 客户端是应用，按照其应用程序 ID 进行标识。 OAuth 客户端通常是与最终用户交互的对象，并向授权服务器请求令牌。 客户端必须获得资源所有者授权才能访问资源。
* **资源服务器**是资源或数据所在的位置。 它信任授权服务器安全验证和授权 OAuth 客户端，并使用持有者访问令牌来确保可以授予对资源的访问权限。

## <a name="app-registration"></a>应用注册

每个想同时接受个人帐户和工作帐户或学校帐户的应用都必须通过“应用注册”体验在 [Azure 门户](https://aka.ms/appregistrations)中进行注册，然后才能使用 OAuth 2.0 或 OpenID Connect 登录这些用户。 应用注册进程会收集一些值并将其分配到应用：

* 用于唯一标识应用的**应用程序 ID**
* 用于将响应定向回到应用的“重定向 URI”（可选）
* 其他一些特定于方案的值。

请了解如何 [注册应用](quickstart-register-app.md)获取详细信息。

## <a name="endpoints"></a>终结点

注册后，应用将通过向终结点发送请求来与 Microsoft 标识平台通信：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

其中 `{tenant}` 可以接受以下四个不同值之一：

| 值 | 说明 |
| --- | --- |
| `common` | 允许用户使用个人 Microsoft 帐户和工作/学校帐户从 Azure AD 登录应用程序。 |
| `organizations` | 仅允许用户使用工作/学校帐户从 Azure AD 登录应用程序。 |
| `consumers` | 仅允许用户使用个人的 Microsoft 帐户 (MSA) 登录应用程序。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` | 仅允许用户使用工作/学校帐户从特定的 Azure AD 租户登录应用程序。 可以使用 Azure AD 租户的友好域名或租户的 GUID 标识符。 |

要了解如何与这些终结点进行交互，请在[协议](#protocols)部分中选择特定的应用类型，然后通过访问链接获取更多信息。

> [!TIP]
> 在 Azure AD 中注册的任何应用都可以使用 Microsoft 标识平台终结点，即使它们不登录个人帐户。  这样，就可以将现有的应用程序迁移到 Microsoft 标识平台和 [MSAL](reference-v2-libraries.md) 而无需重新创建应用程序。

## <a name="tokens"></a>令牌

OAuth 2.0 和 OpenID Connect 广泛使用持有者 **令牌**，通常以 [Jwt (JSON Web 令牌) ](https://tools.ietf.org/html/rfc7519)的形式表示。 持有者令牌是一种轻型安全令牌，它授予对受保护资源的“持有者”访问权限。 从这种意义上来说，"持有者" 是获取令牌副本的任何人。 虽然某一方必须首先通过 Microsoft 标识平台的身份验证才能收到持有者令牌，但如果不采取必要的步骤在传输过程和存储中对令牌进行保护，令牌可能会被意外的某一方拦截并使用。 虽然某些安全令牌具有内置机制来防止未经授权方使用它们，但是持有者令牌没有这一机制，因此必须在安全的通道（例如传输层安全 (HTTPS)）中进行传输。 如果持有者令牌以明文形式传输，则恶意方可以使用中间人攻击来获取令牌并将其用于对受保护资源进行未经授权的访问。 当存储或缓存持有者令牌供以后使用时，也应遵循同样的安全原则。 请始终确保应用以安全的方式传输和存储持有者令牌。 有关持有者令牌的更多安全注意事项，请参阅 [RFC 6750 第 5 部分](https://tools.ietf.org/html/rfc6750)。

在 OAuth 2.0/OIDC 中主要有3种类型的令牌：

* [访问令牌](access-tokens.md) -资源服务器从客户端接收的标记，包含已授予客户端的权限。  
* [ID 令牌](id-tokens.md) -客户端从授权服务器接收的标记，用于登录用户并获取有关它们的基本信息。
* 刷新令牌-客户端使用它来获取新的访问令牌和 ID 令牌。  它们是不透明的字符串，只能由授权服务器理解。

## <a name="protocols"></a>协议

如果已准备好查看一些示例请求，请开始了解以下协议文档之一。 每个教程对应于特定的身份验证方案。 如果在确定适当的流时需要帮助，请查看[可使用 Microsoft 标识平台构建的应用类型](v2-app-types.md)。

* [利用 OAuth 2.0 生成移动应用程序、本机应用程序和 web 应用程序](v2-oauth2-auth-code-flow.md)
* [通过 OpenID Connect 登录用户](v2-protocols-oidc.md)
* [使用 OAuth 2.0 客户端凭据流构建守护程序或服务器端进程](v2-oauth2-client-creds-grant-flow.md)
* [使用 OAuth 2.0 代理流在 Web API 中获取令牌](v2-oauth2-on-behalf-of-flow.md)
* [采用 OAuth 2.0 隐式流构建单页应用](v2-oauth2-implicit-grant-flow.md)
