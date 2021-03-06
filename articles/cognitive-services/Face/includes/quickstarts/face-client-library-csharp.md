---
title: 人脸 .NET 客户端库快速入门
description: 参考本快速入门开始使用用于 .NET 的人脸客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: c243668f4ca1569ad05567649ad4a2498888847c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88602587"
---
适用于 .NET 的人脸客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过人脸服务，可以访问用于检测和识别图像中的人脸的高级算法。

使用适用于 .NET 的人脸客户端库可以：

* [检测图像中的人脸](#detect-faces-in-an-image)
* [查找相似人脸](#find-similar-faces)
* [创建和训练人员组](#create-and-train-a-person-group)
* [识别人脸](#identify-a-face)
* [创建用于数据迁移的快照](#take-a-snapshot-for-data-migration)

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [示例](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>先决条件

* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="创建人脸资源"  target="_blank">创建人脸资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 获取密钥和终结点后，请为该密钥和终结点 URL [创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别命名为 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。 

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `face-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*Program.cs*。 

```dotnetcli
dotnet new console -n face-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```dotnetcli
dotnet build
```

生成输出不应包含警告或错误。 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

在首选的编辑器或 IDE 中，从项目目录打开 *Program.cs* 文件。 然后，添加以下 `using` 指令：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

在应用程序的 `Main` 方法中，为该资源的 Azure 终结点和密钥创建变量。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的人脸客户端库：

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

以下类和接口将处理人脸 .NET 客户端库的某些主要功能：

|名称|说明|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | 此类代表使用人脸服务的授权，使用所有人脸功能时都需要用到它。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。 |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|此类处理可对人脸执行的基本检测和识别任务。 |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|此类代表已从图像中的单个人脸检测到的所有数据。 可以使用它来检索有关人脸的详细信息。|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|此类管理云中存储的 **FaceList** 构造，这些构造存储各种不同的人脸。 |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| 此类管理云中存储的 **Person** 构造，这些构造存储属于单个人员的一组人脸。|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| 此类管理云中存储的 **PersonGroup** 构造，这些构造存储各种不同的 **Person** 对象。 |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|此类管理快照功能。 可以使用它来暂时保存所有基于云的人脸数据，并将这些数据迁移到新的 Azure 订阅。 |

## <a name="code-examples"></a>代码示例

以下代码片段演示如何使用适用于 .NET 的人脸客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [检测图像中的人脸](#detect-faces-in-an-image)
* [查找相似人脸](#find-similar-faces)
* [创建和训练人员组](#create-and-train-a-person-group)
* [识别人脸](#identify-a-face)
* [创建用于数据迁移的快照](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假设已经为人脸密钥和终结点（名为 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`）[创建了环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

在新方法中，使用终结点和密钥实例化客户端。 使用密钥创建一个 **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** 对象，并在终结点中使用该对象创建一个 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 对象。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

你可能想要在 `Main` 方法中调用此方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>声明帮助程序字段

你稍后将添加的几个人脸操作需要以下字段。 在类的根目录中定义以下 URL 字符串。 此 URL 指向示例图像的文件夹。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

定义字符串以指向不同的识别模型类型。 稍后，你将能够指定要用于人脸检测的识别模型。 有关这些选项的信息，请参阅[指定识别模型](../../Face-API-How-to-Topics/specify-recognition-model.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>在图像中检测人脸

将以下方法调用添加到 main 方法。 接下来将定义该方法。 最后的检测操作将使用 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 对象、图像 URL 和识别模型。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>获取检测到的人脸对象

在下一个代码块中，`DetectFaceExtract` 方法将在给定 URL 处的三个图像中检测人脸，并在程序内存中创建 **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** 对象的列表。 **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 值列表指定要提取的特征。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>显示检测到的人脸数据

`DetectFaceExtract` 方法的其余部分将分析和打印每个检测到的人脸的属性数据。 每个属性必须在原始人脸检测 API 调用中单独指定（在 **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 列表中）。 下面的代码处理每个属性，但你可能只需要使用一个或一些属性。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>查找相似人脸

以下代码采用检测到的单个人脸（源），并搜索其他一组人脸（目标），以找到匹配项。 找到匹配项后，它会将匹配的人脸的 ID 输出到控制台。

### <a name="detect-faces-for-comparison"></a>检测人脸以进行比较

首先定义另一个人脸检测方法。 需要先检测图像中的人脸，然后才能对其进行比较；此检测方法已针对比较操作进行优化。 它不会提取以上部分所示的详细人脸属性，而是使用另一个识别模型。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>查找匹配项

以下方法检测一组目标图像和单个源图像中的人脸。 然后，它将比较这些人脸，并查找与源图像类似的所有目标图像。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>输出匹配项

以下代码将匹配详细信息输出到控制台：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>识别人脸

识别操作采用一个或多个人员的图像，并在图像中查找每个人脸的标识。 它将每个检测到的人脸与某个 **PersonGroup**（面部特征已知的不同 **Person** 对象的数据库）进行比较。 为了执行“识别”操作，你首先需要创建并训练 PersonGroup

### <a name="create-and-train-a-person-group"></a>创建和训练人员组

以下代码创建包含六个不同 **Person** 对象的 **PersonGroup**。 它将每个 **Person** 与一组示例图像相关联，然后进行训练以按面部特征识别每个人。 **Person** 和 **PersonGroup** 对象在验证、识别和分组操作中使用。

#### <a name="create-persongroup"></a>创建 PersonGroup

在类的根目录中声明一个字符串变量，用于表示要创建的 **PersonGroup** 的 ID。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

在新方法中添加以下代码。 此方法将执行“识别”操作。 第一个代码块将人员的姓名与其示例图像相关联。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

接下来添加以下代码，以便为字典中的每个人员创建一个 **Person** 对象，并从相应的图像添加人脸数据。 每个 **Person** 对象通过其唯一 ID 字符串来与同一个 **PersonGroup** 相关联。 请记得将变量 `client`、 `url` 和 `RECOGNITION_MODEL1` 传入此方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>训练 PersonGroup

从图像中提取人脸数据并将其分类成不同的 **Person** 对象后，必须训练 **PersonGroup** 才能识别与其每个 **Person** 对象关联的视觉特征。 以下代码调用异步 **train** 方法并轮询结果，然后将状态输出到控制台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

现已准备好在验证、识别或分组操作中使用此 **Person** 组及其关联的 **Person** 对象。

### <a name="get-a-test-image"></a>获取测试图像

请注意，[创建和训练人员组](#create-and-train-a-person-group)中的代码定义了一个 `sourceImageFileName` 变量。 此变量对应于源图像 &mdash; 包含要识别的人员的图像。

### <a name="identify-faces"></a>标识人脸

以下代码采用源映像，创建在图像中检测到的所有人脸的列表。 将会根据 **PersonGroup** 识别这些人脸。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

下一代码片段将调用 IdentifyAsync 操作，并将结果输出到控制台。 此处，服务会尝试将源图像中的每个人脸与给定 **PersonGroup** 中的某个 **Person** 进行匹配。 Identify 方法就此结束。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>创建用于数据迁移的快照

利用快照功能，可将已保存的人脸数据（例如训练的 **PersonGroup**）移到不同的 Azure 认知服务人脸订阅。 例如，如果你使用免费订阅创建了一个 PersonGroup 对象，现在想要将其迁移到付费订阅，则可以使用此功能。 有关快照功能的概述，请参阅[迁移人脸数据](../../Face-API-How-to-Topics/how-to-migrate-face-data.md)。

此示例将迁移你在[创建和训练人员组](#create-and-train-a-person-group)中创建的 **PersonGroup**。 可以先完成该部分，或者创建你自己的要迁移的人脸数据构造。

### <a name="set-up-target-subscription"></a>设置目标订阅

首先，必须有另一个已包含人脸资源的 Azure 订阅；可以遵循[设置](#setting-up)部分中的步骤做好此准备。 

然后，在程序的 `Main` 方法中定义以下变量。 需要为 Azure 帐户的订阅 ID 以及新（目标）帐户的密钥、终结点和订阅 ID 创建新的环境变量。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

对于此示例，请为目标 **PersonGroup**（属于新订阅的对象，可将数据复制到其中）的 ID 声明一个变量。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>对目标客户端进行身份验证

接下来，添加代码以便辅助人脸订阅进行身份验证。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>使用快照

剩余的快照操作必须将在异步方法中进行。 

1. 第一步是**创建**快照，以将原始订阅的人脸数据保存到临时云位置。 此方法返回用于查询操作状态的 ID。

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. 接下来，不断查询该 ID，直到操作完成。

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. 然后使用 **apply** 操作将人脸数据写入目标订阅。 此方法也会返回一个 ID 值。

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. 同样，请不断查询新 ID，直到该操作完成。

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. 最后，完成 try/catch 块并完成该方法。

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

此时，新的 **PersonGroup** 对象应包含与原始对象相同的数据，并且应该可以从新的（目标）Azure 人脸订阅访问该对象。

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

如果你在本快速入门中创建了 **PersonGroup** 并想要删除它，请在程序中运行以下代码：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

使用以下代码定义删除方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

此外，如果你在本快速入门中使用快照功能迁移了数据，则还需要删除已保存到目标订阅的 **PersonGroup**。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用适用于 .NET 的人脸库来执行基本任务。 接下来，请在参考文档中详细了解该库。

> [!div class="nextstepaction"]
> [人脸 API 参考 (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [什么是人脸服务？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) 上找到此示例的源代码。
