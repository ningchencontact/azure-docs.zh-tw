---
title: 在 Visual Studio 中進行應用程式偵錯 | Microsoft Docs
description: 利用本機開發叢集上的 Visual Studio 來開發服務及為服務偵錯，以便改善您服務的可靠性和效能。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 682059914b5d86f5e670e373a4acf3e4ac6246ba
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428222"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>使用 Visual Studio 偵錯 Service Fabric 應用程式
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>偵錯本機 Service Fabric 應用程式
您可以在本機電腦開發叢集中開發和偵錯 Azure Service Fabric 應用程式來節省時間和金錢。 Visual Studio 2019 或 2015年可以部署到本機叢集應用程式，並自動連接到您的應用程式的所有執行個體的 偵錯工具。 Visual Studio 必須偵錯工具連線的系統管理員身分執行。

1. 遵循 [設定 Service Fabric 開發環境](service-fabric-get-started.md)中的步驟來啟動本機開發叢集。
2. 按 **F5** 或按一下 [偵錯]   >  [開始偵錯]  。
   
    ![開始偵錯應用程式][startdebugging]
3. 透過按一下 [偵錯]  功能表中的命令，在您的程式碼中設定中斷點並逐步執行應用程式。
   
   > [!NOTE]
   > Visual Studio 將附加至您應用程式的所有執行個體。 當您逐步執行程式碼時，系統可能會透過多個程序叫用中斷點而導致並行工作階段。 若要嘗試在叫用中斷點之後停用它們，請在執行緒識別碼上條件化每個中斷點，或使用診斷事件。
   > 
   > 
4. [診斷事件]  視窗會自動開啟，讓您可以即時檢視診斷事件。
   
    ![即時檢視診斷事件][diagnosticevents]
5. 您也可以在 [雲端總管] 開啟 [診斷事件]  視窗。  在 [Service Fabric]  之下，以滑鼠右鍵按一下任何節點，然後選擇 [檢視串流追蹤]  。
   
    ![開啟診斷事件視窗][viewdiagnosticevents]
   
    如果您想要篩選追蹤的特定服務或應用程式，啟用串流追蹤該特定服務或應用程式。
6. 您可以在自動產生的 **ServiceEventSource.cs** 檔案中查看診斷事件，並從應用程式程式碼進行呼叫。
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. [診斷事件]  視窗支援篩選、暫停和即時檢查事件。  篩選是事件訊息的簡易字串搜尋，包括其內容。
   
    ![篩選、暫停和繼續，或即時檢查事件][diagnosticeventsactions]
8. 偵錯服務如同偵錯任何其他應用程式。 您通常會設定中斷點，透過 Visual Studio 進行偵錯。 即使可靠集合會在多個節點上複寫，它們仍會實作 IEnumerable。 這項實作表示，您可以使用 [結果] 檢視 Visual Studio 中查看您已儲存在您偵錯時。 若要這樣做，請設定中斷點中的任何程式碼。
   
    ![開始偵錯應用程式][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>偵錯遠端 Service Fabric 應用程式
如果在 Azure 中的 Service Fabric 叢集上執行 Service Fabric 應用程式，您可以從遠端偵錯這些應用程式，直接從 Visual Studio。

> [!NOTE]
> 此功能需要 [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) 和 [Azure SDK for.NET 2.9](https://azure.microsoft.com/downloads/)。    
> 
> 

<!-- -->
> [!WARNING]
> 遠端偵錯適用於開發/測試案例，而非用於生產環境中，因為會對執行中的應用程式造成影響。
> 
> 

1. 瀏覽至您的叢集，在**Cloud Explorer**。 以滑鼠右鍵按一下，然後選擇 **啟用偵錯**
   
    ![啟用遠端偵錯][enableremotedebugging]
   
    此動作將會開始啟用您的叢集節點和所需的網路組態上的遠端偵錯延伸模組的程序。
2. 在 [雲端總管]  中的叢集節點上按一下滑鼠右鍵，然後選擇 [附加偵錯工具] 
   
    ![連結偵錯工具][attachdebugger]
3. 在 [附加至程序]  對話方塊中，選擇您要偵錯的處理序，然後按一下 [附加] 
   
    ![選擇程序][chooseprocess]
   
    您想要附加至的處理序名稱等於您的服務專案組件名稱。
   
    偵錯工具將會複製至執行處理序的所有節點。
   
   * 在您將在偵錯無狀態服務的情況下，所有的執行個體的所有節點上會是服務的偵錯工作階段的一部分。
   * 如果您偵錯具狀態服務時，任何資料分割的主要複本會是作用中，因此偵錯工具攔截。 如果在偵錯工作階段期間移動主要複本，仍會在偵錯工作階段內處理該複本。
   * 若要只攔截相關分割區或特定服務的執行個體，您可以使用條件式中斷點只中斷特定的磁碟分割或執行個體。
     
     ![條件式中斷點][conditionalbreakpoint]
     
     > [!NOTE]
     > 我們目前不支援對多個執行個體具有相同服務可執行檔名稱的 Service Fabric 叢集進行。
     > 
     > 
4. 一旦完成應用程式的偵錯，在 [雲端總管]  中以滑鼠右鍵按一下叢集，然後選擇 [停用偵錯]  ，即可停用遠端偵錯延伸模組
   
    ![停用遠端偵錯][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>從遠端叢集節點串流追蹤
您還可以用於追蹤對資料流直接從 Visual studio 遠端叢集節點。 這項功能可讓您串流在 Service Fabric 叢集節點上產生的 ETW 追蹤事件。

> [!NOTE]
> 此功能需要 [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) 和 [Azure SDK for.NET 2.9](https://azure.microsoft.com/downloads/)。
> 這項功能僅支援在 Azure 中執行的叢集。
> 
> 

<!-- -->
> [!WARNING]
> 串流追蹤適用於開發/測試案例，而非用於生產環境中，因為會對執行中的應用程式造成影響。
> 在生產案例中，您應依賴使用 Azure 診斷轉送事件。
> 
> 

1. 瀏覽至您的叢集，在**Cloud Explorer**。 以滑鼠右鍵按一下，然後選擇 **啟用串流追蹤**
   
    ![啟用遠端串流追蹤][enablestreamingtraces]
   
    此動作會啟動啟用串流追蹤延伸模組，在您的叢集節點，以及所需的網路組態的程序。
2. 展開 [雲端總管]  中的 [節點]  元素，以滑鼠右鍵按一下您要串流追蹤的節點，然後選擇 [檢視串流追蹤] 
   
    ![檢視遠端串流追蹤][viewremotestreamingtraces]
   
    對您想要查看其追蹤的任意數目的節點，重複執行步驟 2。 每個節點串流會顯示在專用視窗中。
   
    您現在可以查看 Service Fabric 所發出的追蹤，以及您的服務。 如果您想要篩選事件，只顯示特定的應用程式，只要在篩選器中輸入應用程式的名稱即可。
   
    ![檢視串流追蹤][viewingstreamingtraces]
3. 完成從您的叢集串流追蹤後，在 [雲端總管]  中以滑鼠右鍵按一下此叢集，選擇 [停用串流追蹤]  ，即可停用遠端串流追蹤
   
    ![停用遠端串流追蹤][disablestreamingtraces]

## <a name="next-steps"></a>後續步驟
* [測試 Service Fabric 服務](service-fabric-testability-overview.md)。
* [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
