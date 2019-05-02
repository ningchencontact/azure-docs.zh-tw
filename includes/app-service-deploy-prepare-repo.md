---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 2b9706241bd65ee75869d8b1fe58c6922d9e246a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951509"
---
## <a name="prepare-your-repository"></a>準備您的存放庫

若要从 Azure 应用服务 Kudu 生成服务器获取自动生成，请确保项目中存储库根路径具有正确的文件。

| 執行階段 | 根目錄檔案 |
|-|-|
| ASP.NET (僅限 Windows) | _*.sln_、_*.csproj_ 或 _default.aspx_ |
| ASP.NET Core | _*.sln_ 或 _*.csproj_ |
| PHP | _index.php_ |
| Ruby (僅限 Linux) | _Gemfile_ |
| Node.js | 含啟動指令碼的 _server.js_、_app.js_, 或 _package.json_ |
| Python (僅限 Windows) | _\*.py_、_requirements.txt_ 或 _runtime.txt_ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_ 或 _iisstart.htm_ |
| WebJobs | _App\_Data/jobs/continuous_ (適用於持續的 WebJobs) 或 _App\_Data/jobs/triggered_ (適用於觸發的 WebJobs) 底下的 _\<job_name>/run.\<extension>_。 有关详细信息，请参阅 [Kudu WebJobs 文档](https://github.com/projectkudu/kudu/wiki/WebJobs)。 |
| Functions | 請參閱 [Azure Functions 的持續部署](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)。 |

若要自訂部署，您可以在儲存機制根路徑中包含 _.deployment_ 檔案。 有关详细信息，请参阅[自定义部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)和[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 如果您是在 Visual Studio 中開發，可讓 [Visual Studio 為您建立存放庫](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio)。 该项目可立即通过 Git 进行部署。
>
>

