---
title: Windows Azure 的 VM 架構的工作流程 |Microsoft Docs
description: 當您部署服務，本文章會提供工作流程處理序的概觀。
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480737"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 傳統 VM 架構的工作流程 
這篇文章提供您部署或更新的 Azure 資源，例如虛擬機器時所發生的工作流程處理程序的概觀。 

> [!NOTE]
>Azure 針對建立和使用資源方面，有二種不同的的部署模型：资源管理器部署模型和经典部署模型。 本文涵蓋之內容包括使用傳統部署模型。

下圖顯示 Azure 資源的架構。

![Azure 的工作流程](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>工作流程的基本概念
   
**答：** RDFE / FFE 是從使用者到網狀架構的通訊路徑。 公開的 API 是前端，管理入口網站和服務管理 API，例如 Visual Studio、 Azure MMC 等等 RDFE （RedDog 前端）。  從使用者的所有要求都通過 RDFE。 FFE （Fabric 前端） 是轉譯到 fabric 命令的要求從 RDFE 的圖層。 從 RDFE 的所有要求都瀏覽到網狀架構控制器 FFE。

**B**。 網狀架構控制器會負責維護及監視資料中心內的所有資源。 它會與 OS 傳送的資訊，例如客體 OS 版本、 服務套件、 服務組態和服務狀態在網狀架構上的 網狀架構主機代理程式通訊。

**C**。 主機代理程式所在主機 OSsystem，而且會負責設定客體 OS，並與客體代理程式 (WindowsAzureGuestAgent) 進行通訊，以更新到未預期的目標狀態的角色，並執行的活動訊號檢查客體代理程式。 如果主機代理程式的 10 分鐘未收到活動訊號回應，主機代理程式會重新啟動客體作業系統。

**C2**。 WaAppAgent 負責安裝、 設定及更新 WindowsAzureGuestAgent.exe。

**D**。  WindowsAzureGuestAgent 會負責下列各項：

1. 設定包括防火牆、 Acl、 LocalStorage 資源、 服務封裝和組態，以及憑證的客體 OS。設定執行角色的使用者帳戶的 SID。
2. 通訊至網狀架構的角色狀態。
3. 啟動 WaHostBootstrapper 和監視它以確定該角色就會處於目標狀態。

**E**。 WaHostBootstrapper 負責：

1. 讀取 「 角色 」 組態，並啟動所有適當的工作和程序來設定及執行的角色。
2. 監視其所有子處理序。
3. 引發 StatusCheck 事件上角色主機處理序。

**F**。 如果角色已設定為完整 IIS web 角色 （它不會執行為 SDK 1.2 HWC 角色），則會執行 IISConfigurator。 它會負責：

1. 啟動標準的 IIS 服務
2. 在 web 組態中設定的重寫模組
3. 如已設定的角色服務模型中的應用程式集區設定
4. 設定 IIS 記錄，以指向 DiagnosticStore LocalStorage 資料夾
5. 設定權限和 Acl
6. 網站位於 %roleroot%:\sitesroot\0 和指向這個位置，以執行 IIS 的應用程式集區中。 

**G**。 啟動工作所定義的角色模型，且 WaHostBootstrapper 開始著手。 啟動工作可以設定為在背景中以非同步方式執行，主機啟動載入器會開始啟動工作，然後繼續其他的啟動工作。 啟動工作也可以設定在主控件啟動載入器會等待啟動工作執行完成，並傳回成功 (0) 的結束碼再繼續進行下一步 啟動工作的簡單 （預設值） 模式下執行。

**H**。 這些工作是 SDK 的一部分，而且會定義為角色的服務定義 (.csdef) 中的外掛程式。 為啟動工作中，展開**DiagnosticsAgent**並**RemoteAccessAgent**是唯一的因為它們都各自定義兩個啟動工作，一個一般，另一個具有 **/blockStartup**參數。 正常啟動工作被定義為背景啟動工作，因此它可以在背景中執行角色本身執行時。 **/BlockStartup**啟動工作定義做為簡單的啟動工作，使 WaHostBootstrapper 等候結束，然後再繼續。 **/BlockStartup**工作等候完成初始化時，一般的工作，然後結束和允許的主機啟動載入器，以繼續。 這麼做是為了讓角色處理序啟動 （這是透過 /blockStartup 工作） 之前，則您可以設定診斷和 RDP 存取。 這也可讓診斷和繼續執行之後，主應用程式啟動載入器已完成 （這是透過一般的工作） 的啟動工作的 RDP 存取。

**我**。 WaWorkerHost 是標準的主機處理序一般背景工作角色。 此主控件程序會裝載角色的所有 Dll 和進入點程式碼，例如 OnStart 和 執行。

**J**。 WaWebHost 是標準的主控件程序，針對 web 角色，如果它們設定為使用 SDK 1.2 相容可裝載 Web 核心 (HWC)。 角色可以根據服務定義 (.csdef) 中移除項目，以啟用 HWC 模式。 在此模式中，所有服務的程式碼和 Dll 執行 WaWebHost 程序。 不使用 IIS (w3wp)，而且有設定在 [IIS 管理員] 中，因為 IIS 會裝載在 WaWebHost.exe 沒有 AppPools。

**K**。 WaIISHost 是使用完整 IIS web 角色的角色進入點程式碼的主控件程序。 此程序會載入第一個找到使用 DLL **RoleEntryPoint**類別，並從這個類別 （OnStart、 執行、 OnStop） 執行的程式碼。 任何**RoleEnvironment** RoleEntryPoint 類別中建立的事件 （例如 StatusCheck 和已變更） 所引發的這項程序。

**L**。 W3WP 是標準 IIS 工作者處理序時所用的角色設定為使用完整 IIS。 這會從 IISConfigurator 執行應用程式集區設定。 在此程序引發會在此處建立的任何 RoleEnvironment 」 事件 （例如 StatusCheck 和已變更）。 請注意，如果您有兩個程序中的事件訂閱，將會引發兩個位置 （WaIISHost 和 w3wp.exe） 中的 RoleEnvironment 事件。

## <a name="workflow-processes"></a>工作流程處理序

1. 使用者提出要求，或上傳.cspkg 和.cscfg 檔案，告訴資源以停止進行組態變更，例如，依此類推。 這可以透過 Azure 入口網站或使用服務管理 API，例如 Visual Studio 發行功能的工具來完成。 此要求會在所有訂用帳戶相關工作，，然後進行通訊的要求 FFE，送到 RDFE 執行。 這些工作流程步驟的其餘部分會以部署新的封裝，並啟動它。
2. FFE 尋找正確的電腦集區 （根據客戶意見，為同質群組或地理位置加上從網狀架構，例如機器可用性的輸入），並在該電腦集區中的主要的網狀架構控制器通訊。
3. 網狀架構控制器會尋找具有可用的 CPU 核心的主機 （或拋註冊新的主控件）。 服務封裝和組態會複製到主機和網狀架構控制器進行通訊與主機上的代理程式的主機作業系統部署套件 （設定 Dip 連接埠、 客體作業系統，等等）。
4. 主機代理程式會啟動客體作業系統，並與客體代理程式 (WindowsAzureGuestAgent) 通訊。 主應用程式會將活動訊號傳送至來賓，藉此確定角色致力於其目標狀態。
5. WindowsAzureGuestAgent 設定客體作業系統 （如防火牆、 Acl、 LocalStorage，等等），將新的 XML 組態檔複製到 c:\Config，，然後開啟 WaHostBootstrapper 程序。
6. 針對完整的 IIS web 角色，WaHostBootstrapper 啟動 IISConfigurator，並告訴它要從 IIS 刪除任何現有 AppPools web 角色。
7. 讀取 WaHostBootstrapper**啟動**從 E:\RoleModel.xml 工作，並開始執行啟動工作。 WaHostBootstrapper 會等到所有的簡單啟動工作完成並傳回 「 成功 」 訊息。
8. 針對完整的 IIS web 角色，WaHostBootstrapper 告訴 IISConfigurator 設定 IIS 應用程式集區和點的站台`E:\Sitesroot\<index>`，其中`<index>`是 0 起始的索引數目成<Sites>服務定義的項目。
9. WaHostBootstrapper 會啟動主控件程序，根據角色類型：
    1. **背景工作角色**:WaWorkerHost.exe 已啟動。 WaHostBootstrapper 執行 onstart （） 方法。它會傳回之後，WaHostBootstrapper 開始執行 run （） 方法中，然後同時標示為就緒角色並將它放到負載平衡器循環 （如果已定義 InputEndpoints）。 WaHostBootsrapper 接著會進入迴圈，以檢查角色狀態。
    1. **SDK 1.2 HWC Web 角色**:WaWebHost 會啟動。 WaHostBootstrapper 執行 onstart （） 方法。 它會傳回之後，WaHostBootstrapper 執行 run （） 方法，就會開始同時標示為就緒角色並將它放入負載平衡器輪替。 WaWebHost 發出暖機要求 (GET /do.rd_runtime_init)。 所有 web 要求會都傳送至 WaWebHost.exe。 WaHostBootsrapper 接著會進入迴圈，以檢查角色狀態。
    1. **完整 IIS Web 角色**: aIISHost 已啟動。 WaHostBootstrapper 執行 onstart （） 方法。 它會傳回之後，它執行 run （） 方法，就會開始和同時標示為就緒角色並將它放入負載平衡器輪替。 WaHostBootsrapper 接著會進入迴圈，以檢查角色狀態。
10. 傳入網頁要求為完整的 IIS web 角色的觸發程序來啟動 W3WP 處理序並處理要求，會在內部部署 IIS 環境中相同的 IIS。

## <a name="log-file-locations"></a>記錄檔位置

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
此記錄檔包含服務包括啟動、 停止和新的組態變更。 如果服務不會變更，您可以預期看到的時間在這個記錄檔很大的間距。
- C:\Logs\WaAppAgent.Log.  
此記錄檔包含狀態更新與活動訊號通知，並更新每隔 2-3 秒。  此記錄檔包含執行個體的狀態歷程記錄檢視，並會告訴您執行個體時未處於就緒狀態。
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**IIS 記錄**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Windows 事件記錄**

D:\Windows\System32\Winevt\Logs
 



