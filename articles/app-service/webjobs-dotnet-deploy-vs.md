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
ms.openlocfilehash: b8cc628ef7db198c5068bb3917cf41113ba1687a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417096"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>使用 Visual Studio 開發和部署 WebJob - Azure App Service

這篇文章說明如何使用 Visual Studio 部署至 web 應用程式的主控台應用程式專案[App Service](overview.md)作為[Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226)。 如需如何使用 [Azure 入口網站](https://portal.azure.com)部署 WebJob 的相關資訊，請參閱[使用 WebJob 執行背景工作](webjobs-create.md)。

您可以發佈多個 WebJobs 的單一 web 應用程式。 請確定每個 WebJob 的 web 應用程式中都有唯一名稱。

版本的 3.x [Azure WebJobs SDK](webjobs-sdk-how-to.md)可讓您開發.NET Core 應用程式或.NET Framework 版本 2.x 支援.NET Framework 的應用程式，執行的 WebJobs。 您部署 WebJobs 專案的方式是針對與.NET Framework 的.NET Core 專案不同。

## <a name="webjobs-as-net-core-console-apps"></a>為.NET Core 主控台應用程式的 WebJobs

當使用版本 3.x 的 Webjob，您可以建立並發行為.NET Core 主控台應用程式的 Webjob。 若要建立及發佈.NET Core 主控台應用程式至 Azure 以 webjob 的逐步指示，請參閱[開始使用適用於事件驅動的背景處理的 Azure WebJobs SDK](webjobs-sdk-get-started.md)。

> [!NOTE]
> .NET core WebJobs 無法連結 web 專案。 如果您需要部署您的 web 應用程式的 WebJob，您應該[建立您的 WebJob，.NET Framework 主控台應用程式](#webjobs-as-net-framework-console-apps)。  

### <a name="deploy-to-azure-app-service"></a>部署至 Azure App Service

從 Visual Studio 發佈至 App Service 的.NET Core WebJob 發佈 ASP.NET Core 應用程式使用相同的工具。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob 類型

根據預設，WebJob 會發佈從.NET Core 主控台專案觸發時才會執行或依需求。 您也可以更新專案，以[依排程執行](#scheduled-execution)或連續執行。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>排程的執行

當您發行至 Azure，.NET Core 主控台應用程式時的新*settings.job*檔案加入至專案。 若要設定執行排程的 WebJob 中使用此檔案。 如需詳細資訊，請參閱 <<c0> [ 排程觸發的 WebJob](#scheduling-a-triggered-webjob)。

#### <a name="continuous-execution"></a>連續執行

若要變更 Alwayson 是 Azure 中啟用時，持續執行 WebJob，您可以使用 Visual Studio。

1. 如果您還沒有這麼做，請[將專案發佈至 Azure](#deploy-to-azure-app-service)。

1. 在 [方案總管]  中，以滑鼠右鍵按一下專案並選取 [發佈]  。

1. 在 **發佈**索引標籤上，選擇**設定**。 

1. 中**設定檔設定** 對話方塊中，選擇**連續**的**WebJob 類型**，然後選擇**儲存**。

    ![發行設定 對話方塊中的 webjob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 選取 **發佈**重新發行更新的設定與 WebJob。

## <a name="webjobs-as-net-framework-console-apps"></a>為.NET Framework 主控台應用程式的 WebJobs  

當 Visual Studio 部署 WebJobs 功能的.NET Framework 主控台應用程式專案時，它會執行兩項工作：

* 將執行階段檔案複製到適當的資料夾中的 web 應用程式 (*App_Data/作業/continuous*連續 webjobs 並*App_Data/作業/triggered*排程或隨選 webjobs)。
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

如果您使用 Visual Studio 2019，安裝[Azure 開發工作負載](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads)。

### <a id="convert"></a>啟用現有主控台應用程式專案的 WebJobs 部署

您有兩個選擇：

* [透過 Web 專案啟用自動部署](#convertlink)。

  設定現有主控台應用程式專案，以便在您部署 Web 專案時，它會以 WebJob 的方式自動部署。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

* [不透過 Web 專案啟用部署](#convertnolink)。

  設定現有主控台應用程式專案以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。

#### <a id="convertlink"></a> 透過 Web 專案啟用自動 WebJobs 部署

1. 以滑鼠右鍵按一下 [方案總管]  中的 Web 專案，然後依序按一下 [新增]   > [Existing Project as Azure WebJob]  。
   
    ![Existing Project as Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 在 [專案名稱]  下拉式清單中，選取要新增為 WebJob 的主控台應用程式專案。
   
    ![Selecting project in Add Azure WebJob dialog](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. 完成 [[Add Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]  。 

#### <a id="convertnolink"></a> 不透過 Web 專案啟用 WebJobs 部署
1. 以滑鼠右鍵按一下 [方案總管]  中的主控台應用程式專案，然後按一下 [發行為 Azure WebJob]  。 
   
    ![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [[加入 Azure WebJob]](#configure) 對話方塊隨即出現，而且 [專案名稱]  方塊中已選取此專案。
2. 完成 [[加入 Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]  。
   
   此時會出現 [發行 Web]  精靈。  如果您不打算立即發行，請關閉精靈。 您所輸入的設定會被儲存下來，以供[部署專案](#deploy)時使用。

### <a id="create"></a>建立具有 WebJobs 功能的新專案
若要建立具有 WebJobs 功能的新專案，您可以使用主控台應用程式專案範本，並如 [上一節](#convert)中所述來啟用 WebJobs 部署。 另一種方式是，您可以使用 WebJobs 新專案範本：

* [在獨立的 WebJob 中使用 WebJobs 新專案範本](#createnolink)
  
    建立專案，並將專案設定為以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 為了調整不受 Web 應用程式資源影響的 WebJob 資源，您可能會想執行此動作。
* [在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本](#createlink)
  
    建立專案，並設定在針對位於相同解決方案中的 Web 專案進行部署時，會自動以 WebJob 的方式部署此專案。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

> [!NOTE]
> WebJobs 新專案範本自動安裝 NuGet 套件，並包括適用於 *WebJobs SDK* 的 [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)程式碼。 如果您不想使用 WebJobs SDK，請移除或變更 *Program.cs* 中的 `host.RunAndBlock` 陳述式。
> 
> 

#### <a id="createnolink"></a> 在獨立的 WebJob 中使用 WebJobs 新專案範本
1. 依序按一下 [檔案]   > [新增專案]  ，然後在 [新增專案]  對話方塊中，依序按一下 [雲端]   > [Azure WebJob (.NET Framework)]  。
   
    ![顯示 WebJob 範本的 [新增專案] 對話方塊](./media/webjobs-dotnet-deploy-vs/np.png)
2. 請依照稍早所示的指示，[將主控台應用程式專案設定成獨立的 WebJobs 專案](#convertnolink)。

#### <a id="createlink"></a> 在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本
1. 以滑鼠右鍵按一下 [方案總管]  中的 Web 專案，然後依序按一下 [新增]   > [New Azure WebJob Project]  。
   
    ![New Azure WebJob Project menu entry](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 完成 [[Add Azure WebJob]](#configure) 對話方塊，然後按一下 [確定]  。

### <a id="configure"></a>[新增 Azure WebJob] 對話方塊
[加入 Azure WebJob]  對話方塊可讓您輸入 WebJob 名稱和 WebJob 的執行模式設定。 

![[加入 Azure WebJob] 對話方塊](./media/webjobs-dotnet-deploy-vs/aaw2.png)

此對話方塊中的欄位會對應至 Azure 入口網站中 [新增 WebJob]  對話方塊上的欄位。 如需詳細資訊，請參閱[使用 WebJobs 執行背景工作](webjobs-create.md)。

> [!NOTE]
> * 如需命令列部署的詳細資訊，請參閱[啟用 Azure WebJobs 的命令列或連續傳遞](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。
> * 如果您部署了 WebJob，但之後決定變更 WebJob 的類型並重新部署，就必須刪除 webjobs-publish-settings.json  檔案。 這樣會讓 Visual Studio 再次顯示發佈選項，您才能夠變更 WebJob 的類型。
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

若要自我部署 WebJobs 專案，請以滑鼠右鍵按一下 [方案總管]  中的專案，然後按一下 [發行為 Azure WebJob]  。 

![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

若是獨立的 WebJob，則 Web 專案所使用的相同 [發行 Web]  精靈隨即出現，但其中幾個設定可以變更。

## <a name="scheduling-a-triggered-webjob"></a>排程觸發的 WebJob

使用 WebJobs *settings.job*檔案來判斷執行 WebJob 時。 若要設定執行排程的 WebJob 中使用此檔案。 下列範例會每小時執行上午 9 點到下午 5 點：

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

這個檔案必須位於 [WebJobs] 資料夾的根目錄，沿著端 WebJob 的指令碼，例如`wwwroot\app_data\jobs\triggered\{job name}`或`wwwroot\app_data\jobs\continuous\{job name}`。 當您從 Visual Studio 部署 WebJob 時，請將您的 `settings.job` 檔案屬性標示為 [有更新時才複製]  。 

當您[從 Azure 入口網站中建立 WebJob](webjobs-create.md)，為您建立 settings.job 檔案。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON 運算式

WebJobs 會使用相同的 CRON 運算式排程為在 Azure Functions 計時器觸發程序。 若要深入了解 CRON 支援，請參閱[計時器觸發程序參考文章](../azure-functions/functions-bindings-timer.md#cron-expressions)。

### <a name="settingjob-reference"></a>setting.job 參考

WebJobs 支援下列設定：

| **設定** | **類型**  | **說明** |
| ----------- | --------- | --------------- |
| `is_in_place` | 全部 | 可讓工作就地執行而不第一次複製到暫存資料夾。 若要進一步了解，請參閱[WebJobs 工作目錄](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)。 |
| `is_singleton` | 連續 | 只執行 Webjob 時相應放大的單一執行個體。若要進一步了解，請參閱[設定為 singleton 的連續工作](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)。 |
| `schedule` | 觸發 | CRON 排程上執行的 WebJob。 若要進一步了解，請參閱[計時器觸發程序參考文章](../azure-functions/functions-bindings-timer.md#cron-expressions)。 |
| `stopping_wait_time`| 全部 | 可讓您控制的關閉行為。 若要進一步了解，請參閱[正常關機](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)。 |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 WebJobs SDK](webjobs-sdk-how-to.md)
