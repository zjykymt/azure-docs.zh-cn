---
title: 配置 Linux Python 应用
description: 了解如何为应用配置预先构建的 Python 容器。 本文介绍最常见的配置任务。
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: 88a3876757c3f51d1fc70692976987560ee1b991
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961629"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>为 Azure 应用服务配置 Linux Python 应用

本文介绍 [Azure 应用服务](overview.md)如何运行 Python 应用，以及如何按需自定义应用服务的行为。 必须连同所有必需的 [pip](https://pypi.org/project/pip/) 模块一起部署 Python 应用。

部署 [Git 存储库](deploy-local-git.md)或部署启用了生成过程的 [Zip 包](deploy-zip.md)时，应用服务部署引擎会自动激活虚拟环境并运行 `pip install -r requirements.txt`。

对于在应用服务中使用内置 Linux 容器的 Python 开发人员，本指南为其提供了关键概念和说明。 若从未使用过 Azure 应用服务，则首先应按照 [Python 快速入门](quickstart-python.md)以及[将 Python 与 PostgreSQL 配合使用教程](tutorial-python-postgresql-app.md)进行操作。

> [!NOTE]
> Linux 是目前在应用服务中用于运行 Python 应用的推荐选项。 有关 Windows 选项的信息，请参阅 [Windows 风格的应用服务上的 Python](/visualstudio/python/managing-python-on-azure-app-service)。
>

## <a name="show-python-version"></a>显示 Python 版本

若要显示当前的 Python 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有受支持的 Python 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

生成自己的容器映像可以运行不受支持的 Python 版本。 有关详细信息，请参阅[使用自定义 Docker 映像](tutorial-custom-container.md?pivots=container-linux)。

## <a name="set-python-version"></a>设置 Python 版本

在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，将 Python 版本设置为 3.7：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>自定义生成自动化

如果在启用生成自动化的情况下使用 Git 或 zip 包部署应用，应用服务生成自动化将按以下顺序完成各个步骤：

1. 运行 `PRE_BUILD_SCRIPT_PATH` 指定的自定义脚本。
1. 运行 `pip install -r requirements.txt`。
1. 如果在存储库的根目录中找到了 *manage.py*，则运行 *manage.py collectstatic*。 但是，如果 `DISABLE_COLLECTSTATIC` 设置为 `true`，则会跳过此步骤。
1. 运行 `POST_BUILD_SCRIPT_PATH` 指定的自定义脚本。

`PRE_BUILD_COMMAND`、`POST_BUILD_COMMAND` 和 `DISABLE_COLLECTSTATIC` 是默认为空的环境变量。 若要运行生成前命令，请定义 `PRE_BUILD_COMMAND`。 若要运行生成后命令，请定义 `POST_BUILD_COMMAND`。 若要在生成 Django 应用时禁用正在运行的 collectstatic，请设置 `DISABLE_COLLECTSTATIC=true`。

以下示例在一系列命令中指定两个以逗号分隔的变量。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关用于自定义生成自动化的其他环境变量，请参阅 [Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有关应用服务如何在 Linux 中运行和生成 Python 应用的详细信息，请参阅 [Oryx 文档：如何检测和生成 Python 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)。

## <a name="container-characteristics"></a>容器特征

部署到 Linux 上的应用服务的 Python 应用在[应用服务 Python GitHub 存储库](https://github.com/Azure-App-Service/python)中定义的 Docker 容器内运行。 可以在特定于版本的目录中找到映像配置。

此容器具有以下特征：

- 应用是结合附加参数 `--bind=0.0.0.0 --timeout 600`，使用 [Gunicorn WSGI HTTP Server](https://gunicorn.org/) 运行的。

- 默认情况下，基础映像包含 Flask Web 框架，但容器支持符合 WSGI 规范并与 Python 3.7 兼容的其他框架，例如 Django。

- 若要安装其他包（例如 Django），请使用 `pip freeze > requirements.txt` 在项目的根目录中创建 [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) 文件。 然后，使用 Git 部署将项目发布到应用服务，这会在容器中自动运行 `pip install -r requirements.txt` 来安装应用的依赖项。

## <a name="container-startup-process"></a>容器启动过程

在启动期间，Linux 上的应用服务容器将运行以下步骤：

1. 使用[自定义启动命令](#customize-startup-command)（如果已提供）。
2. 检查是否存在 [Django 应用](#django-app)，如果已检测到，请为其启动 Gunicorn。
3. 检查是否存在 [Flask 应用](#flask-app)，如果已检测到，请为其启动 Gunicorn。
4. 如果未找到其他任何应用，则启动容器中内置的默认应用。

以下部分提供了每个选项的更多详细信息。

### <a name="django-app"></a>Django 应用

对于 Django 应用，应用服务将在应用代码中查找名为 `wsgi.py` 的文件，然后使用以下命令运行 Gunicorn：

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

若要更精细地控制启动命令，请使用[自定义启动命令](#customize-startup-command)，并将 `<module>` 替换为包含 *wsgi.py* 的模块名称。

### <a name="flask-app"></a>Flask 应用

对于 Flask，应用服务将查找名为 *application.py* 或 *app.py* 的文件并启动 Gunicorn，如下所示：

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

如果主应用模块包含在不同的文件中，请对应用对象使用不同的名称；若要为 Gunicorn 提供附加的参数，请使用[自定义启动命令](#customize-startup-command)。

### <a name="default-behavior"></a>默认行为

如果应用服务找不到自定义命令、Django 应用或 Flask 应用，则它会运行位于 _opt/defaultsite_ 文件夹中的默认只读应用。 默认应用如下所示：

![Linux 上的默认应用服务网页](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>自定义启动命令

可以通过提供自定义 Gunicorn 启动命令来控制容器的启动行为。 为此，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

例如，如果 Flask 应用的主模块是 *hello.py*，而该文件中的 Flask 应用对象名为 `myapp`，则 *\<custom-command>* 如下所示：

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

如果主模块位于子文件夹（例如 `website`）中，请使用 `--chdir` 参数指定该文件夹：

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

还可以将 Gunicorn 的任何附加参数添加到 *\<custom-command>* ，例如 `--workers=4`。 有关详细信息，请参阅[运行 Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org)。

要使用非 Gunicorn 服务器（例如 [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)），可以将 *\<custom-command>* 替换为以下内容：

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> 应用服务将忽略处理自定义命令文件时出现的任何错误，然后通过查找 Django 和 Flask 应用来继续执行其启动过程。 如果未看到预期的行为，请检查启动文件是否已部署到应用服务且不包含任何错误。

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](configure-common.md#configure-app-settings)。 然后，可以使用标准的 [os.environ](https://docs.python.org/3/library/os.html#os.environ) 模式访问这些设置。 例如，若要访问名为 `WEBSITE_SITE_NAME` 的应用设置，请使用以下代码：

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果应用逻辑需要检查用户请求是否已加密，可以检查 `X-Forwarded-Proto` 标头。

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

使用常用 Web 框架可以访问采用标准应用模式的 `X-Forwarded-*` 信息。 在 [CodeIgniter](https://codeigniter.com/) 中，[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) 默认检查 `X_FORWARDED_PROTO` 的值。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑难解答

- **部署自己的应用代码后看到默认应用。** 之所以显示默认应用，是因为并未将应用代码部署到应用服务，或者应用服务未找到应用代码，因此运行了默认应用。
- 请重启应用服务，等待 15 到 20 秒，然后再次检查应用。
- 请确保使用适用于 Linux 的应用服务，而不要使用基于 Windows 的实例。 在 Azure CLI 中运行 `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` 命令，对 `<resource_group_name>` 和 `<app_service_name>` 进行相应的替换。 应该会看到作为输出的 `app,linux`，否则请重新创建应用服务并选择 Linux。
- 使用 SSH 或 Kudu 控制台直接连接到应用服务，并检查文件是否存在于 *site/wwwroot* 下。 如果这些文件不存在，请检查部署过程并重新部署应用。
- 如果这些文件存在，则表示应用服务无法识别特定的启动文件。 检查是否按应用服务的预期方式为 [Django](#django-app) 或 [Flask](#flask-app) 构建了应用，或使用[自定义启动命令](#customize-startup-command)。
- **浏览器中显示“服务不可用”消息。** 浏览器在等待应用服务的响应时超时，这表示应用服务已启动 Gunicorn 服务器，但指定应用代码的参数不正确。
- 刷新浏览器，尤其是在应用服务计划中使用最低定价层的情况下。 例如，使用免费层时，应用可能需要较长时间才能启动，并在刷新浏览器后才会做出响应。
- 检查是否按应用服务的预期方式为 [Django](#django-app) 或 [Flask](#flask-app) 构建了应用，或使用[自定义启动命令](#customize-startup-command)。
- [访问日志流](#access-diagnostic-logs)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 PostgreSQL 的 Python 应用](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [教程：从专用容器存储库进行部署](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](faq-app-service-linux.md)