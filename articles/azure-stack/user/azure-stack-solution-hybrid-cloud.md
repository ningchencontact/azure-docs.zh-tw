---
title: 使用 Azure 與 Azure Stack 部署混合式雲端 | Microsoft Docs
description: 了解如何使用 Azure 與 Azure Stack 部署混合式雲端。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 1629c4b62fb04e057c38261a33fd3bc759b279c1
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267397"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>教學課程：使用 Azure 與 Azure Stack 部署混合式雲端解決方案

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

本教學課程說明如何部署使用 Azure 公用雲端和 Azure Stack 私人雲端的混合式雲端解決方案。

藉由使用混合式雲端解決方案，您將可結合私人雲端在合規性方面的優勢與公用雲端的延展性。 此外，您的開發人員也可善用 Microsoft 開發人員生態系統，並將其技能運用在雲端和內部部署環境中。

## <a name="overview-and-assumptions"></a>概觀與假設

您可以依照本教學課程設定工作流程，讓開發人員將相同的 Web 應用程式部署至公用雲端和私人雲端。 此應用程式將可存取裝載於私人雲端上的非網際網路可路由網路。 這些 Web 應用程式會受到監視，且在流量暴增時，將會以程式修改 DNS 記錄，以將流量重新導向至公用雲端。 當流量降至暴增之前的水準時，流量就會恢復為路由至私人雲端。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> - 部署混合式連線 SQL Server 資料庫伺服器。
> - 將全域 Azure 中的 Web 應用程式連線至混合式網路。
> - 設定跨雲端縮放的 DNS。
> - 設定跨雲端縮放的 SSL 憑證。
> - 設定及部署 Web 應用程式。
> - 建立流量管理員設定檔，並進行其跨雲端縮放的設定。
> - 針對增加的流量設定 Application Insights 監視和警示。
> - 設定全域 Azure 和 Azure Stack 之間的自動流量切換。

### <a name="assumptions"></a>假設

本教學課程假設您擁有全域 Azure 和 Azure Stack 的基本知識。 若要在開始本教學課程前深入了解，請檢閱下列文章：

 - [Azure 簡介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

本教學課程也假設您已有 Azure 訂用帳戶。 如果您沒有訂用帳戶，可以先[建立免費帳戶](https://azure.microsoft.com/free/)，再開始操作。

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，請確定您符合下列需求：

- Azure Stack 開發套件 (ASDK) 或 Azure Stack 整合系統的訂用帳戶。 若要部署 Azure Stack 開發套件，請依照[使用安裝程式部署 ASDK](../asdk/asdk-deploy.md) 中的指示操作。
- 您的 Azure Stack 安裝應安裝下列項目：
  - Azure App Service。 請使用 Azure Stack 操作員來部署和設定 Azure App Service 環境。 在本教學課程中，App Service 至少必須有一 (1) 個可用的專用背景工作角色。
  - Windows Server 2016 映像
  - 具有 Microsoft SQL Server 映像的 Windows Server 2016
  - 適當的方案和供應項目
 - Web 應用程式的網域名稱。 如果您沒有網域名稱，可以向 GoDaddy、Bluehost 和 InMotion 等網域提供者購買。
- 信任的憑證授權單位 (例如 LetsEncrypt) 為您的網域核發的 SSL 憑證。
- 與 SQL Server 資料庫通訊且支援 Application Insights 的 Web 應用程式。 您可以從 GitHub 下載 [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) 範例。
- Azure 虛擬網路與 Azure Stack 虛擬網路之間的混合式網路。 如需詳細指示，請參閱[使用 Azure 和 Azure Stack 設定混合式雲端連線](azure-stack-solution-hybrid-connectivity.md)。

- 在 Azure Stack 上具有私用組建代理程式的混合式持續整合/持續部署 (CI/CD) 管線。 如需詳細指示，請參閱[使用 Azure 和 Azure Stack 應用程式設定混合式雲端身分識別](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>部署混合式連線 SQL Server 資料庫伺服器

1. 登入 Azure Stack 使用者入口網站。

2. 在 [儀表板] 上選取 [Marketplace]。

    ![Azure Stack Marketplace](media/azure-stack-solution-hybrid-cloud/image1.png)

3. 在 [Marketplace] 中選取 [計算]，然後選擇 [其他]。 在 [其他] 底下，選取**免費的 SQL Server 授權：Windows Server 上的 SQL Server 2017 Developer** 映像。

    ![選取虛擬機器映像](media/azure-stack-solution-hybrid-cloud/image2.png)

4. 在**免費的 SQL Server 授權：Windows Server 上的 SQL Server 2017 Developer** 上，選取 [建立]。

5. 在 [基本 > 設定基本設定] 上，提供虛擬機器 (VM) 的 [名稱]、SQL Server SA 的 [使用者名稱]，和 SA 的 [密碼]。  從 [訂用帳戶]  下拉式清單中，選取要部署到的訂用帳戶。 針對 [資源群組] 使用 [選擇現有的]，並將 VM 放在與 Azure Stack Web 應用程式相同的資源群組中。

    ![設定虛擬機器的基本設定](media/azure-stack-solution-hybrid-cloud/image3.png)

6. 在 [大小] 下方，選擇您的 VM 大小。 在本教學課程中，建議您使用 A2_Standard 或 DS2_V2_Standard。

7. 在 [設定 > 設定選用功能] 下方，進行下列設定：

    - **儲存體帳戶**。 如有需要，請建立新帳戶。
    - **虛擬網路**

      > [!Important]  
      > 請確定您的 SQL Server VM 部署在與 VPN 閘道相同的虛擬網路上。

    - **公用 IP 位址**。 您可以使用預設設定。
    - **網路安全性群組** (NSG)。 建立新的 NSG。
    - **擴充功能和監視**。 請保留預設設定值。
    - **診斷儲存體帳戶**。 如有需要，請建立新帳戶。
    - 選取 [確定] 以儲存您的設定。

    ![設定選用功能](media/azure-stack-solution-hybrid-cloud/image4.png)

1. 在 [SQL Server 設定] 下方，進行下列設定：
   - 針對 [SQL 連線能力]，選取 [公用 (網際網路)]。
   - 將 [連接埠] 保留為預設值 **1433**。
   - 針對 [SQL 驗證]，選取 [啟用]。

     > [!Note]  
     > SQL 驗證啟用時，應會自動填入您在 [基本] 中設定的 "SQLAdmin" 資訊。

   - 其餘設定請保留為預設值。 選取 [確定] 。

    ![進行 SQL Server 設定](media/azure-stack-solution-hybrid-cloud/image5.png)

9. 在 [摘要] 上檢閱虛擬機器設定，然後選取 [確定] 開始進行部署。

    ![設定摘要](media/azure-stack-solution-hybrid-cloud/image6.png)

10. 建立新的 VM 需要一些時間。 您可以在 [虛擬機器] 中檢視 VM 的 [狀態]。

    ![虛擬機器](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中建立 Web 應用程式

Azure App Service 可簡化執行和管理 Web 應用程式的工作。 Azure Stack 與 Azure 是一致的，因此 App Service 在這兩種環境中都可執行。 您將使用 App Service 來裝載應用程式。

### <a name="create-web-apps"></a>建立 Web 應用程式

1. 依照[管理 Azure 中的 App Service 方案](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)中的指示，在 Azure 中建立 Web 應用程式。 請確實將 Web 應用程式放在與混合式網路相同的訂用帳戶和資源群組中。

2. 在 Azure Stack 中重複前述步驟 (1)。

### <a name="add-route-for-azure-stack"></a>新增 Azure Stack 的路由

Azure Stack 上的 App Service 必須可從公用網際網路進行路由，讓使用者能夠存取您的應用程式。 如果您的 Azure Stack 可從網際網路存取，請記下 Azure Stack Web 應用程式的公眾 IP 位址或 URL。

如果您使用 ASDK，您可以[設定靜態 NAT 對應](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal)，將 App Service 公開於虛擬環境外。

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>將 Azure 中的 Web 應用程式連線至混合式網路

若要讓 Azure 中的 Web 前端與 Azure Stack 中的 SQL Server 資料庫能夠相互連線，Web 應用程式必須連線至 Azure 和 Azure Stack 之間的混合式網路。 若要啟用連線，您必須：

- 設定點對站連線
- 設定 Web 應用程式
- 修改 Azure Stack 中的區域網路閘道。

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>設定點對站連線所需的 Azure 虛擬網路

在混合式網路中，Azure 端的虛擬網路閘道必須允許點對站連線，以便與 Azure App Service 整合。

1. 在 Azure 中，瀏覽至 [虛擬網路閘道] 頁面。 在 [設定] 下方，選取 [點對站設定]。

    ![點對站選項](media/azure-stack-solution-hybrid-cloud/image8.png)

2. 選取 [立即設定]，進行點對站設定。

    ![開始進行點對站設定](media/azure-stack-solution-hybrid-cloud/image9.png)

3. 在 [點對站] 設定頁面上，將您要使用的私人 IP 位址範圍輸入 [位址集區] 中。

   > [!Note]  
   > 請確定您指定的範圍並未與混合式網路的全域 Azure 或 Azure Stack 元件中的子網路已使用的任何位址範圍重疊。

   在 [通道類型] 下方，取消核取 [IKEv2 VPN]。 選取 [儲存] 以完成點對站設定。

   ![點對站設定](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>整合 Azure App Service 應用程式與混合式網路

1. 若要將應用程式連線至 Azure VNet，請依照[啟用 VNet 整合](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration)中的指示操作。

2. 找出裝載 Web 應用程式的 App Service 方案，並瀏覽至其 [設定]。 在 [設定] 中，選取 [網路]。

    ![設定網路功能](media/azure-stack-solution-hybrid-cloud/image11.png)

3. 在 [VNET 整合] 中，選取 [按一下這裡進行管理]。

    ![管理 VNET 整合](media/azure-stack-solution-hybrid-cloud/image12.png)

4. 選取您要設定的 VNET。 在 [路由至 VNET 的 IP 位址] 下方，輸入 Azure VNet、Azure Stack VNet 和點對站位址空間的 IP 位址範圍。 選取 [儲存] 以驗證並儲存這些設定。

    ![路由的 IP 位址範圍](media/azure-stack-solution-hybrid-cloud/image13.png)

若要深入了解 how App Service 與 Azure VNet 的整合方式，請參閱[將您的應用程式與 Azure 虛擬網路整合](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)。

### <a name="configure-the-azure-stack-virtual-network"></a>設定 Azure Stack 虛擬網路

必須設定 Azure Stack 虛擬網路中的區域網路閘道，以路由來自 App Service 點對站位址範圍的流量。

1. 在 Azure Stack 中，瀏覽至 [區域網路閘道]。 在 [設定] 下方，選取 [設定]。

    ![閘道設定選項](media/azure-stack-solution-hybrid-cloud/image14.png)

2. 在 [位址空間] 中，輸入 Azure 虛擬網路閘道的點對站位址範圍。選取 [儲存] 以驗證並儲存此設定。

    ![點對站位址空間](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>設定跨雲端縮放的 DNS

藉由適當設定跨雲端應用程式的 DNS，使用者將可存取您 Web 應用程式的全域 Azure 和 Azure Stack 執行個體。 本教學課程中的 DNS 設定也可讓 Azure 流量管理員在負載增加或減少時路由流量。

本教學課程使用 Azure DNS 來管理 DNS。 (App Service 網域不適用)。

### <a name="create-subdomains"></a>建立子網域

由於流量管理員需倚賴 DNS CNAME，因此子網域必須能夠正確地將流量路由至端點。 如需與 DNS 記錄和網域之間的對應有關的詳細資訊，請參閱[使用流量管理員對應網域](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

針對 Azure 端點，您必須建立可讓使用者用來存取 Web 應用程式的子網域。 在本教學課程中可以使用 **app.northwind.com**，但您應根據自己的網域自訂此值。

您也必須以 Azure Stack 端點的 A 記錄建立子網域。 您可以使用 **azurestack.northwind.com**。

### <a name="configure-a-custom-domain-in-azure"></a>在 Azure 中設定自訂網域

1. 藉由[將 CNAME 對應至 Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)，以將 **app.northwind.com** 主機名稱新增至 Azure Web 應用程式。

### <a name="configure-custom-domains-in-azure-stack"></a>在 Azure Stack 中設定自訂網域

1. 藉由[將 A 記錄對應至 Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record)，以將 **azurestack.northwind.com** 主機名稱新增至 Azure Stack Web 應用程式。 對於 App Service 應用程式請使用網際網路可路由的 IP 位址。

2. 藉由[將 CNAME 對應至 Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)，以將 **app.northwind.com** 主機名稱新增至 Stack Azure Web 應用程式。 請使用您在先前的步驟 (1) 中設定的主機名稱作為 CNAME 的目標。

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>設定跨雲端縮放的 SSL 憑證

您必須確定 Web 應用程式所收集的敏感性資料在傳輸至 SQL 資料庫和在該處待用時都受到良好的保護。

您將設定 Azure 和 Azure Stack Web 應用程式，以對所有傳入的流量使用 SSL 憑證。

### <a name="add-ssl-to-azure-and-azure-stack"></a>將 SSL 新增至 Azure 和 Azure Stack

若要將 SSL 新增至 Azure：

1. 確定您取得的 SSL 憑證適用於您所建立的子網域。 (您也可以使用萬用字元憑證)。

2. 在 Azure 中，依照[將現有的自訂 SSL 憑證繫結至 Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) 一文的**準備您的 Web 應用程式**和**繫結 SSL 憑證**小節所提供的指示操作。 請選取 [以 SNI 為基礎的 SSL] 作為 [SSL 類型]。

3. 將所有流量重新都導向至 HTTPS 連接埠。 請依照[將現有的自訂 SSL 憑證繫結至 Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) 一文的**強制執行 HTTPS** 小節所提供的指示操作。

若要將 SSL 新增至 Azure Stack：

- 重複您用於 Azure 的步驟 1-3。

## <a name="configure-and-deploy-the-web-application"></a>設定及部署 Web 應用程式

您將設定應用程式程式碼以將遙測資料報告至正確的 Application Insights 執行個體，並使用正確的連接字串來設定 Web 應用程式。 若要深入了解 Application Insights，請參閱[什麼是 Application Insights？](https://docs.microsoft.com/azure/application-insights/app-insights-overview)。

### <a name="add-application-insights"></a>新增 Application Insights

1. 在 Microsoft Visual Studio 中開啟您的 Web 應用程式。

2. 在您的專案中[新增 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry)，以傳輸 Application Insights 在 Web 流量增加或減少時用來建立警示的遙測資料。

### <a name="configure-dynamic-connection-strings"></a>設定動態連接字串

Web 應用程式的每個執行個體會使用不同的方法連線至 SQL 資料庫。 在 Azure 中的應用程式會使用 SQL Server 虛擬機器 (VM) 的私人 IP 位址，而 Azure Stack 中的應用程式則會使用 SQL Server VM 的公用 IP 位址。

> [!Note]  
> 在 Azure Stack 整合系統中，公用 IP 位址不應是可用網際網路路由的。 在 Azure Stack 開發套件 (ASDK) 上，公用 IP 位址無法在 ASDK 以外路由。

您可以使用 App Service 環境變數將不同的連接字串傳至應用程式的每個執行個體。

1. 在 Visual Studio 中開啟應用程式。

2. 開啟 Startup.cs 並且尋找下列程式碼區塊：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. 將上述程式碼區塊取代為下列程式碼，以使用 appsettings.json 檔案中定義的連接字串：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>設定 App Service 應用程式設定

1. 建立適用於 Azure 和 Azure Stack 的連接字串。 這兩個字串除了所使用的 IP 位址外，其餘部分應相同。

2. 在 Azure 和 Azure Stack 中，以 `SQLCONNSTR\_` 作為名稱中的前置詞，為 Web 應用程式新增適當的連接字串[作為應用程式設定](https://docs.microsoft.com/azure/app-service/web-sites-configure)。

3. **儲存** Web 應用程式設定，並重新啟動應用程式。

## <a name="enable-automatic-scaling-in-global-azure"></a>在全域 Azure 中啟用自動調整

當您在 App Service 環境中建立 Web 應用程式時，它最初會有一個執行個體。 您可以自動相應放大以新增執行個體，為應用程式提供更多計算資源。 同樣地，您也可以自動相應縮小並減少應用程式所需的執行個體數目。

> [!Note]  
> 您必須以 App Service 方案來設定相應放大和相應縮小。 如果您沒有方案，請先建立方案，再開始執行後續步驟。

### <a name="enable-automatic-scale-out"></a>啟用自動相應放大

1. 在 Azure 中，找出要相應放大的網站所使用的 App Service 方案，然後選取 [相應放大 (App Service 方案)]。

    ![相應放大](media/azure-stack-solution-hybrid-cloud/image16.png)

2. 選取 [啟用自動調整]。

    ![啟用自動調整](media/azure-stack-solution-hybrid-cloud/image17.png)

3. 輸入 [自動調整設定名稱] 的名稱。 選取 [依據計量調整規模]，作為**預設**自動調整規則。 將 [執行個體限制] 設為**最小值：1**、**最大值：10** 和**預設值：1**。

    ![設定自動調整](media/azure-stack-solution-hybrid-cloud/image18.png)

4. 選取 [+新增規則]。

5. 在 [計量來源] 中，選取 [目前的資源]。 請為規則使用下列準則和動作。

**準則**

1. 在 [時間彙總] 下方，選取 [平均]。

2. 在 [計量名稱] 下方，選取 [CPU 百分比]。

3. 在 [運算子] 下方，選取 [大於]。

   - 將 [閾值] 設為 **50**。
   - 將 [持續時間] 設為 **10**。

**Action**

1. 在 [作業] 下方，選取 [將計數增加]。

2. 將 [執行個體計數] 設為 **2**。

3. 將 [緩和時間] 設為 **5**。

4. 選取 [新增] 。

5. 選取 [+新增規則]。

6. 在 [計量來源] 中，選取 [目前的資源]。

   > [!Note]  
   > 目前的資源會包含您 App Service 方案的名稱/GUID，而 [資源類型] 和 [資源] 下拉式清單將會變成灰色。

### <a name="enable-automatic-scale-in"></a>啟用自動相應縮小

當流量減少時，Azure Web 應用程式可以自動減少作用中的執行個體數目，以降低成本。 此動作的主動性低於相應放大，意在盡量降低應用程式使用者所受到的影響。

1. 瀏覽至**預設**相應放大條件，選取 [+ 新增規則]。 請為規則使用下列準則和動作。

**準則**

1. 在 [時間彙總] 下方，選取 [平均]。

2. 在 [計量名稱] 下方，選取 [CPU 百分比]。

3. 在 [運算子] 下方，選取 [小於]。

   - 將 [閾值] 設為 **30**。
   - 將 [持續時間] 設為 **10**。

**Action**

1. 在 [作業] 下方，選取 [將計數減少]。

   - 將 [執行個體計數] 設為 **1**。
   - 將 [緩和時間] 設為 **5**。

2. 選取 [新增] 。

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>建立流量管理員設定檔並設定跨雲端縮放

您將在 Azure 中建立流量管理員設定檔，然後設定端點以啟用跨雲端縮放。

### <a name="create-traffic-manager-profile"></a>建立流量管理員設定檔

1. 選取 [建立資源]
2. 選取 [網路功能]
3. 選取 [流量管理員設定檔] 並進行下列設定：

   - 在 [名稱] 中，輸入設定檔的名稱。 此名稱在 trafficmanager.net 區域內**必須**是唯一的，並且用來建立新的 DNS 名稱 (例如 northwindstore.trafficmanager.net)。
   - 針對 [路由方法]，選取 [加權]。
   - 針對 [訂用帳戶]，選取您要用來建立此設定檔的訂用帳戶。
   - 在 [資源群組]中，為此設定檔建立新的資源群組。
   - 在 [資源群組位置] 中，選取資源群組的位置。 這項設定是指資源群組的位置，完全不影響將部署到全球的流量管理員設定檔。

4. 選取 [建立] 。

    ![建立流量管理員設定檔](media/azure-stack-solution-hybrid-cloud/image19.png)

 流量管理員設定檔在完成全域部署後，會顯示在其建立後所屬資源群組的資源清單中。

### <a name="add-traffic-manager-endpoints"></a>新增流量管理員端點

1. 搜尋您所建立的流量管理員設定檔。 (如果您瀏覽至設定檔的資源群組，請選取設定檔)。

2. 在 [流量管理員設定檔] 的 [設定] 下方，選取 [端點]。

3. 選取 [新增] 。

4. 在 [新增端點] 中，使用 Azure Stack 的下列設定：

   - 針對 [類型]，選取 [外部端點]。
   - 輸入此端點的 [名稱]。
   - 針對 [完整網域名稱 (FQDN) 或 IP]，輸入 Azure Stack Web 應用程式的外部 URL。
   - 將 [權數] 保留為預設值 **1**。 此權數會使所有流量都傳送至此端點 (如果其狀況良好)。
   - 將 [新增為已停用] 保持為未核取。

5. 選取 [確定] 以儲存 Azure Stack 端點。

接下來您將設定 Azure 端點。

1. 在 [流量管理員設定檔] 上，選取 [端點]。
2. 選取 [+新增]。
3. 在 [新增端點] 上，使用 Azure 的下列設定：

   - 針對 [類型]，選取 [Azure 端點]。
   - 輸入此端點的 [名稱]。
   - 針對 [目標資源類型]，選取 [App Service]。
   - 針對 [目標資源] 選取 [選擇 App Service]，以顯示相同訂用帳戶中的 Web Apps 清單。
   - 在 [資源] 中，挑選您想要新增為第一個端點的應用程式服務。
   - 針對 [權數]，選取 **2**。 這會使所有的流量在主要端點狀況不良時均傳送至此端點，或者，您可以使用經觸發會重新導向流量的規則/警示。
   - 將 [新增為已停用] 保持為未核取。

4. 選取 [確定] 以儲存 Azure 端點。

兩個端點都設定之後，將會在您選取 [端點] 時列於 [流量管理員設定檔] 中。 下列螢幕擷取畫面中的範例顯示兩個端點及其各自的狀態和設定資訊。

![端點](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>設定 Application Insights 監視和警示

Azure Application Insights 可讓您根據自己設定的條件來監視應用程式及傳送警示。 其範例包括：應用程式無法使用、發生失敗狀況，或顯示效能問題。

您將使用 Application Insights 計量來建立警示。 當這些警示觸發時，Web 應用程式執行個體將會自動從 Azure Stack 切換至 Azure 以相應放大，然後再切換回 Azure Stack 以相應縮小。

### <a name="create-an-alert-from-metrics"></a>建立以計量為依據的警示

請瀏覽至本教學課程的資源群組，然後選取 Application Insights 執行個體以開啟 **Application Insights**。

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

您將使用下列檢視建立相應放大警示和相應縮小警示。

### <a name="create-the-scale-out-alert"></a>建立相應放大警示

1. 在 [設定] 下方，選取 [警示 (傳統)]。
2. 選取 [新增計量警示 (傳統)]。
3. 在 [新增規則] 中，進行下列設定：

   - 針對 [名稱]，輸入 [高載至 Azure 雲端]。
   - [描述] 是選擇性的。
   - 在 [來源]、[警示標的] 下方，選取 [計量]。
   - 在 [準則] 下方選取您的訂用帳戶、流量管理員設定檔的資源群組，和資源的流量管理員設定檔名稱。

4. 針對 [計量]，選取 [要求率]。
5. 針對 [條件]，選取 [大於]。
6. 輸入**2** 作為 [閾值]。
7. 針對 [期間]，選取 [過去 5 分鐘內]。
8. 在 [通知方式] 下方：
   - 核取 [電子郵件擁有者、參與者和讀取者] 的核取方塊。
   - 輸入 [其他系統管理員電子郵件] 的電子郵件地址。

9. 在功能表列上，選取 [儲存]。

### <a name="create-the-scale-in-alert"></a>建立擴展集警示

1. 在 [設定] 下方，選取 [警示 (傳統)]。
2. 選取 [新增計量警示 (傳統)]。
3. 在 [新增規則] 中，進行下列設定：

   - 針對 [名稱]，輸入 [調整回 Azure Stack]。
   - [描述] 是選擇性的。
   - 在 [來源]、[警示標的] 下方，選取 [計量]。
   - 在 [準則] 下方選取您的訂用帳戶、流量管理員設定檔的資源群組，和資源的流量管理員設定檔名稱。

4. 針對 [計量]，選取 [要求率]。
5. 針對 [條件]，選取 [小於]。
6. 輸入**2** 作為 [閾值]。
7. 針對 [期間]，選取 [過去 5 分鐘內]。
8. 在 [通知方式] 下方：
   - 核取 [電子郵件擁有者、參與者和讀取者] 的核取方塊。
   - 輸入 [其他系統管理員電子郵件] 的電子郵件地址。

9. 在功能表列上，選取 [儲存]。

下列螢幕擷取畫面顯示相應放大和相應縮小的警示。

   ![警示 (傳統)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>在 Azure 與 Azure Stack 之間重新導向流量

您可以設定 Web 應用程式流量在 Azure 與 Azure Stack 之間的手動或自動切換。

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>設定 Azure 與 Azure Stack 之間的手動切換

當網站達到您所設定的閾值時，您將收到警示。 請使用下列步驟以手動方式將流量重新導向至 Azure。

1. 在 Azure 入口網站中，選取您的流量管理員設定檔。

    ![流量管理員端點](media/azure-stack-solution-hybrid-cloud/image20.png)

2. 選取 [端點]。
3. 選取 [Azure 端點]。
4. 在 [狀態] 下方選取 [已啟用]，然後選取 [儲存]。

    ![啟用 Azure 端點](media/azure-stack-solution-hybrid-cloud/image23.png)

5. 在流量管理員設定檔的 [端點] 上，選取 [外部端點]。
6. 在 [狀態] 下方選取 [已停用]，然後選取 [儲存]。

    ![停用 Azure Stack 端點](media/azure-stack-solution-hybrid-cloud/image24.png)

設定端點之後，應用程式流量將會傳送至您的 Azure 相應放大 Web 應用程式，而不是 Azure Stack Web 應用程式。

 ![已變更的端點](media/azure-stack-solution-hybrid-cloud/image25.png)

若要將流量反轉回 Azure Stack，請使用先前的步驟執行下列動作：

- 啟用 Azure Stack 端點
- 停用 Azure 端點

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>設定 Azure 與 Azure Stack 之間的自動切換

如果您的應用程式執行於 Azure Functions 所提供的[無伺服器](https://azure.microsoft.com/overview/serverless-computing/)環境中，您也可以使用 Application Insights 監視。

在此案例中，您可以設定 Application Insights 以使用 Webhook 呼叫函式應用程式。 此應用程式會自動啟用或停用端點，以回應警示。

若要設定自動流量切換，請依據下列步驟操作。

1. 建立 Azure 函式應用程式。
2. 建立由 HTTP 觸發的函式。
3. 匯入資源管理員、Web 應用程式和流量管理員的 Azure SDK。
4. 開發下列作業的程式碼：

   - 向您的 Azure 訂用帳戶驗證。
   - 使用參數切換流量管理員端點，以將流量導向至 Azure 或 Azure Stack。

5. 儲存您的程式碼，並將含有適當參數的函式應用程式 URL 新增至 Application Insights 警示規則設定的 [Webhook] 區段。
6. Application Insights 警示引發時，流量會自動重新導向。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 雲端模式，請參閱[雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns)。
