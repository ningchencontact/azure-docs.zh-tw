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
ms.openlocfilehash: 7ec4028c319749b6a3da019e1d320d3937e9c4b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133161"
---
## <a name="prepare-your-repository"></a>準備您的存放庫

若要從 Azure App Service Kudu 組建伺服器取得自動建置，請確定您儲存機制的根目錄有正確的檔案，在您的專案。

| 執行階段 | 根目錄檔案 |
|-|-|
| ASP.NET (僅限 Windows) | _*.sln_、 _*.csproj_ 或 _default.aspx_ |
| ASP.NET Core | _*.sln_ 或 _*.csproj_ |
| PHP | _index.php_ |
| Ruby (僅限 Linux) | _Gemfile_ |
| Node.js | 含啟動指令碼的 _server.js_、_app.js_, 或 _package.json_ |
| Python | _\*.py_、_requirements.txt_ 或 _runtime.txt_ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_ 或 _iisstart.htm_ |
| WebJobs | _App\_Data/jobs/continuous_ (適用於持續的 WebJobs) 或 _App\_Data/jobs/triggered_ (適用於觸發的 WebJobs) 底下的 _\<job_name>/run.\<extension>_ 。 如需詳細資訊，請參閱 < [Kudu WebJobs 文件](https://github.com/projectkudu/kudu/wiki/WebJobs)。 |
| 函式 | 請參閱 [Azure Functions 的持續部署](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)。 |

若要自訂部署，您可以在儲存機制根路徑中包含 _.deployment_ 檔案。 如需詳細資訊，請參閱 <<c0> [ 來自訂部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)並[自訂部署指令碼](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 如果您是在 Visual Studio 中開發，可讓 [Visual Studio 為您建立存放庫](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio)。 專案是立即準備好使用 Git 進行部署。
>
>

