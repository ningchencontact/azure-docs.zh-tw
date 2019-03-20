---
title: 使用 Visual Studio 開發和部署 WebJob - Azure
description: 了解如何使用 Visual Studio 開發 Azure WebJob，並將其部署至 Azure App Service。
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ede7e2fe3a2ab4c0dfd4efaea5ec789924968194
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750152"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>使用 Visual Studio 開發和部署 WebJob - Azure App Service

本文介绍如何使用 Visual Studio 将控制台应用程序项目作为 [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226) 部署到[应用服务](overview.md)中的 Web 应用。 如需如何使用 [Azure 入口網站](https://portal.azure.com)部署 WebJob 的相關資訊，請參閱[使用 WebJob 執行背景工作](webjobs-create.md)。

可将多个 WebJob 发布到单个 Web 应用。 请确保 Web 应用中的每个 WebJob 具有唯一的名称。

[Azure WebJobs SDK](webjobs-sdk-how-to.md) 版本 3.x 可用于开发作为 .NET Core 应用运行的 WebJob，而版本 2.x 仅支持 .NET Framework。 部署 WebJob 项目的方式根据是该项目是 .NET Core 项目还是 .NET Framework 项目而有所不同。

## <a name="webjobs-as-net-core-console-apps"></a>用作 .NET Core 控制台应用的 WebJob

使用 WebJobs 版本 3.x 时，可以创建并发布用作 .NET Core 控制台应用的 WebJob。 有关创建 .NET Core 控制台应用程序并将其作为 WebJob 发布到 Azure 的分步说明，请参阅[用于事件驱动式后台处理的 Azure WebJobs SDK 入门](webjobs-sdk-get-started.md)。

> [!NOTE]
> .NET Core WebJob 无法与 Web 项目相链接。 如果需要将 WebJob 与 Web 应用一起部署，应该[创建用作 .NET Framework 控制台应用的 WebJob](#webjobs-as-net-framework-console-apps)。  

### <a name="deploy-to-azure-app-service"></a>部署到 Azure 应用服务

通过 Visual Studio 将 .NET Core WebJob 发布到应用服务所用的工具，与发布 ASP.NET Core 应用所用的工具相同。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob 類型

默认情况下，从 .NET Core 控制台项目发布的 WebJob 只会在触发后才会运行，或者按需运行。 也可以将项目更新为[按计划运行](#scheduled-execution)或连续运行。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>按计划执行

将 .NET Core 控制台应用程序发布到 Azure 时，会将一个新的 *settings.job* 文件添加到项目。 使用此文件可以设置 WebJob 的执行计划。 有关详细信息，请参阅[计划触发的 WebJob](#scheduling-a-triggered-webjob)。

#### <a name="continuous-execution"></a>连续执行

可以使用 Visual Studio 来更改 WebJob，以便在 Azure 中启用 Always On 后连续运行该 WebJob。

1. [将项目发布到 Azure](#deploy-to-azure-app-service)（如果尚未这样做）。

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案並選取 [發佈]。

1. 在“发布”选项卡中选择“设置”。 

1. 在“配置文件设置”对话框中，为“WebJob 类型”选择“连续”，然后选择“保存”。

    ![WebJob 的“发布设置”对话框](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 选择“发布”，以使用更新的设置重新发布 WebJob。

## <a name="webjobs-as-net-framework-console-apps"></a>用作 .NET Framework 控制台应用的 WebJob  

当 Visual Studio 部署支持 WebJobs 的 .NET Framework 控制台应用程序项目时，会执行两个任务：

* 将运行时文件复制到 Web 应用中的相应文件夹（对于连续运行的 WebJob，该文件夹为 *App_Data/jobs/continuous*，对于按计划运行或按需运行的 WebJob，该文件夹为 *App_Data/jobs/triggered*）。
* 為已排定在特定時間執行的 WebJobs 設定 [Azure 排程器](https://docs.microsoft.com/azure/scheduler/)工作。 (無需為連續 WebJobs 執行此動作。)

具有 WebJobs 功能的專案會新增下列項目：

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝。
* 包含部署和排程器設定的 [webjob-publish-settings.json](#publishsettings) 檔案。 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

您可以將這些項目新增至現有的主控台應用程式專案，或使用範本建立具有 WebJobs 功能的新主控台應用程式專案。 

以 WebJob 的方式自我部署專案，或將專案連結到 Web 專案，因此每當您要部署 Web 專案時，專案便會自動部署。 若要連結專案，Visual Studio 會在 Web 專案的 [webjobs-list.json](#webjobslist) 檔案中加上具有 WebJobs 功能的專案名稱。

![Diagram showing WebJob project linking to web project](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>必要條件

如果您是使用 Visual Studio 2015，請安裝 [Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/)。

如果您是使用 Visual Studio 2017，請安裝 [Azure 開發工作負載](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads)。

### <a id="convert"></a>啟用現有主控台應用程式專案的 WebJobs 部署

您有兩個選擇：

* [透過 Web 專案啟用自動部署](#convertlink)。

  設定現有主控台應用程式專案，以便在您部署 Web 專案時，它會以 WebJob 的方式自動部署。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

* [不透過 Web 專案啟用部署](#convertnolink)。

  設定現有主控台應用程式專案以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 为了能够缩放不受 Web 应用程序资源影响的 Web 作业资源，可能要执行此操作。

#### <a id="convertlink"></a> 透過 Web 專案啟用自動 WebJobs 部署

1. 以滑鼠右鍵按一下 [方案總管] 中的 Web 專案，然後依序按一下 [新增] > [Existing Project as Azure WebJob]。
   
    ![Existing Project as Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 在 [專案名稱]  下拉式清單中，選取要新增為 WebJob 的主控台應用程式專案。
   
    ![在“添加 Azure Web 作业”对话框中选择项目](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. 完成 [[Add Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]。 

#### <a id="convertnolink"></a> 不透過 Web 專案啟用 WebJobs 部署
1. 以滑鼠右鍵按一下 [方案總管] 中的主控台應用程式專案，然後按一下 [發行為 Azure WebJob]。 
   
    ![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [[加入 Azure WebJob]](#configure) 對話方塊隨即出現，而且 [專案名稱] 方塊中已選取此專案。
2. 完成 [[加入 Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]。
   
   此時會出現 [發行 Web] 精靈。  如果您不打算立即發行，請關閉精靈。 您所輸入的設定會被儲存下來，以供[部署專案](#deploy)時使用。

### <a id="create"></a>建立具有 WebJobs 功能的新專案
若要建立具有 WebJobs 功能的新專案，您可以使用主控台應用程式專案範本，並如 [上一節](#convert)中所述來啟用 WebJobs 部署。 另一種方式是，您可以使用 WebJobs 新專案範本：

* [在獨立的 WebJob 中使用 WebJobs 新專案範本](#createnolink)
  
    建立專案，並將專案設定為以 WebJob 的方式自我部署，且不具 Web 專案的連結。 要在 Web 应用中运行 Web 作业，且 Web 应用中没有正在运行的 Web 应用程序时，请使用此选项。 為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。
* [在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本](#createlink)
  
    建立專案，並設定在針對位於相同解決方案中的 Web 專案進行部署時，會自動以 WebJob 的方式部署此專案。 要在运行相关 Web 应用程序的同一 Web 应用中运行 Web 作业时，请使用此选项。

> [!NOTE]
> Web 作业新建项目模板会自动安装 NuGet 包，并在 *Program.cs* 中包含适用于 [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) 的代码。 如果您不想使用 WebJobs SDK，請移除或變更 *Program.cs* 中的 `host.RunAndBlock` 陳述式。
> 
> 

#### <a id="createnolink"></a> 在獨立的 WebJob 中使用 WebJobs 新專案範本
1. 依序按一下 [檔案] > [新增專案]，然後在 [新增專案] 對話方塊中，依序按一下 [雲端] > [Azure WebJob (.NET Framework)]。
   
    ![顯示 WebJob 範本的 [新增專案] 對話方塊](./media/webjobs-dotnet-deploy-vs/np.png)
2. 請依照稍早所示的指示，[將主控台應用程式專案設定成獨立的 WebJobs 專案](#convertnolink)。

#### <a id="createlink"></a> 在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本
1. 以滑鼠右鍵按一下 [方案總管] 中的 Web 專案，然後依序按一下 [新增] > [New Azure WebJob Project]。
   
    ![New Azure WebJob Project menu entry](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 完成 [[Add Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]。

### <a id="configure"></a>添加 Azure Web 作业对话框
[加入 Azure WebJob] 對話方塊可讓您輸入 WebJob 名稱和 WebJob 的執行模式設定。 

![[加入 Azure WebJob] 對話方塊](./media/webjobs-dotnet-deploy-vs/aaw2.png)

此對話方塊中的欄位會對應至 Azure 入口網站中 [新增 WebJob] 對話方塊上的欄位。 如需詳細資訊，請參閱[使用 WebJobs 執行背景工作](webjobs-create.md)。

> [!NOTE]
> * 如需命令列部署的詳細資訊，請參閱[啟用 Azure WebJobs 的命令列或連續傳遞](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。
> * 如果您部署了 WebJob，但之後決定變更 WebJob 的類型並重新部署，就必須刪除 webjobs-publish-settings.json 檔案。 這樣會讓 Visual Studio 再次顯示發佈選項，您才能夠變更 WebJob 的類型。
> * 如果部署 WebJob，並在稍後將執行模式從連續變更為非連續 (或相反情形)，則在您重新部署時，Visual Studio 會在 Azure 中建立新的 WebJob。 如果您變更其他排程設定但保持執行模式不變，或在排程和隨選模式之間切換，則 Visual Studio 會更新現有的工作，而非建立新的工作。
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
設定 WebJobs 部署的主控台應用程式時，Visual Studio 會安裝 [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝，並將排程資訊儲存在 WebJobs 專案中專案 *Properties* 資料夾的 *webjob-publish-settings.json* 檔案。 以下是該檔案的範例：

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。 檔案結構描述會儲存在 [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json)，您可以在該處進行檢視。  

### <a id="webjobslist"></a>webjobs-list.json
當您將具有 WebJobs 功能的專案連結到 Web 專案時，Visual Studio 會將 WebJobs 專案的名稱儲存在 Web 專案中 *Properties* 資料夾的 *webjobs-list.json* 檔案。 此清單可能包含多個 WebJobs 專案，如下列範例所示：

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。 檔案結構描述會儲存在 [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json)，您可以在該處進行檢視。

### <a id="deploy"></a>部署 WebJobs 專案
已連結到 Web 專案的 WebJobs 專案會透過 Web 專案自動部署。 如需 Web 專案部署的相關資訊，請參閱左邊功能窗格的**使用說明指南** > **部署應用程式**。

若要自我部署 WebJobs 專案，請以滑鼠右鍵按一下 [方案總管] 中的專案，然後按一下 [發行為 Azure WebJob]。 

![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

若是獨立的 WebJob，則 Web 專案所使用的相同 [發行 Web]  精靈隨即出現，但其中幾個設定可以變更。

## <a name="scheduling-a-triggered-webjob"></a>计划触发的 WebJob

WebJobs 使用 *settings.job* 文件确定某个 WebJob 是否已运行。 使用此文件可以设置 WebJob 的执行计划。 以下示例从上午 9 点到下午 5 点每隔一小时运行：

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

此文件必须位于 WebJobs 文件夹的根目录下，与 WebJob 的脚本（例如 `wwwroot\app_data\jobs\triggered\{job name}` 或 `wwwroot\app_data\jobs\continuous\{job name}`）放在一起。 當您從 Visual Studio 部署 WebJob 時，請將您的 `settings.job` 檔案屬性標示為 [有更新時才複製]。 

[从 Azure 门户创建 WebJob](webjobs-create.md) 时，系统会自动创建 settings.job 文件。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON 運算式

WebJobs 使用的 CRON 计划表达式与 Azure Functions 中的计时器触发器相同。 若要详细了解 CRON 支持，请参阅[计时器触发器参考文章](../azure-functions/functions-bindings-timer.md#cron-expressions)。

### <a name="settingjob-reference"></a>setting.job 参考

WebJobs 支持以下设置：

| **設定** | **類型**  | **說明** |
| ----------- | --------- | --------------- |
| `is_in_place` | 全部 | 允许作业在原地运行，而无需首先将其复制到临时文件夹。 有关详细信息，请参阅 [WebJobs 工作目录](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)。 |
| `is_singleton` | 連續 | 仅在横向扩展的单个实例上运行 WebJob。有关详细信息，请参阅[将连续作业设为单一实例](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)。 |
| `schedule` | 觸發 | 根据基于 CRON 的计划运行 WebJob。 有关详细信息，请参阅[计时器触发器参考文章](../azure-functions/functions-bindings-timer.md#cron-expressions)。 |
| `stopping_wait_time`| 全部 | 允许控制关闭行为。 有关详细信息，请参阅[正常关闭](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)。 |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 WebJobs SDK](webjobs-sdk-how-to.md)
