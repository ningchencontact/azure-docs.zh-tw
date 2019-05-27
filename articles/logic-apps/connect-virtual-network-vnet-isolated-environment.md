---
title: 透過整合服務環境 (ISE) 從 Azure Logic Apps 連線至 Azure 虛擬網路
description: 建立整合服務環境 (ISE) 可讓邏輯應用程式與整合帳戶可以存取 Azure 虛擬網路 (VNET)，同時維持私用並獨立於公用或「全域」Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: bd1f06c93a75673f86f0c52f78cad8a60f7a1a1e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65961443"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>透過使用整合服務環境 (ISE) 從 Azure Logic Apps 連線至 Azure 虛擬網路

針對您邏輯應用程式與整合帳戶需存取 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的案例，建立[整合服務的環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是私用和隔離的環境會使用專用的儲存體和保留不同於公用或 「 全域 」 Logic Apps 服務的其他資源。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。 您的 ISE 會「插入」Azure 虛擬網路，然後將 Logic Apps 服務部署到您的虛擬網路。 當您建立邏輯應用程式或整合帳戶時，請選取此 ISE 作為其位置。 然後，您的邏輯應用程式或整合帳戶就可以直接存取虛擬網路中的資源，例如：虛擬機器 (VM)、伺服器、系統及服務。

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

本文會示範如何完成這些工作：

* 請確定虛擬網路上任何必要的連接埠已開啟，使流量可以流經您整合服務的環境 (ISE) 透過子網路在該虛擬網路中。

* 建立您的整合服務環境 (ISE)。

* 建立可以在您 ISE 中執行的邏輯應用程式。

* 在您的 ISE 中建立邏輯應用程式的整合帳戶。

如需整合服務環境的詳細資訊，請參閱[從 Azure Logic Apps 存取 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。

  > [!IMPORTANT]
  > Logic apps、 內建的觸發程序、 內建動作和執行 ISE 使用定價方案的耗用量為基礎的定價方案從不同的連接器。 如需詳細資訊，請參閱 [Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。

* [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。 如果您沒有虛擬網路，請了解如何[建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。 

  * 您的虛擬網路必須有四個*空*個子網路以便進行部署，並在您的 ISE 中建立資源。 您可以事先建立這些子網路，或您可以等到建立其中您可以建立子網路，同時您 ISE。 深入了解[子網路需求](#create-subnet)。 
  
    > [!NOTE]
    > 如果您使用[ExpressRoute](../expressroute/expressroute-introduction.md)，以提供 Microsoft 雲端服務的私人連線，您必須[建立路由表](../virtual-network/manage-route-table.md)具有下列路由傳送，並將該資料表連結供您 ISE 每個子網路：
    > 
    > **名稱**: <*路由名稱*><br>
    > **位址前置詞**:0.0.0.0/0<br>
    > **下一個躍點**：網際網路

  * 請確定您的虛擬網路[提供這些連接埠](#ports)讓您 ISE 可正常運作，並保持可存取。

* 如果您想要用於您的 Azure 虛擬網路中的自訂 DNS 伺服器[遵循下列步驟設定這些伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)您 ISE 部署到虛擬網路之前。 否則每次變更您的 DNS 伺服器時，您也必須重新啟動您的 ISE，這是 ISE 公開預覽版本可取得的功能。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

<a name="ports"></a>

## <a name="check-network-ports"></a>檢查網路連接埠

當您使用整合服務環境 (ISE) 與虛擬網路時，常見的安裝程式發生問題就具有一或多個連接埠遭到封鎖。 您用來建立您的 ISE 與目標系統之間的連線的連接器也可能具有自己的連接埠需求。 例如，如果您使用 FTP 連接器與 FTP 系統進行通訊，請確定可取得您在該 FTP 系統使用的連接埠 (如傳送命令的連接埠 21)。

若要跨虛擬網路的子網路，您將部署 ISE 控制的流量，您可以設定[網路安全性群組](../virtual-network/security-overview.md)依[篩選子網路間的網路流量](../virtual-network/tutorial-filter-network-traffic.md)。 不過，您的 ISE 必須開啟 虛擬網路使用網路安全性群組上的特定連接埠。 如此一來，您 ISE 保持可存取，可以會使您不無法存取您的 ISE 正確運作。 否則，如果任何必要的連接埠無法使用，您 ISE 便會停止運作。

這些表格描述您的 ISE 在您的虛擬網路中使用的連接埠及其用途。 [Resource Manager 服務標籤](../virtual-network/security-overview.md#service-tags)代表一組協助建立安全性規則時，將複雜性降至最低的 IP 位址首碼。

> [!IMPORTANT]
> 在您的子網路內的內部通訊，ISE 會需要您開啟這些子網路內的所有連接埠。

| 目的 | Direction | 連接埠 | 來源服務標籤 | 目的地服務標籤 | 注意 |
|---------|-----------|-------|--------------------|-------------------------|-------|
| 來自 Azure Logic Apps 的通訊 | 輸出 | 80 和 443 | VirtualNetwork | 網際網路 | 取決於外部的服務與 Logic Apps 服務通訊的連接埠 |
| Azure Active Directory | 輸出 | 80 和 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure 儲存體相依性 | 輸出 | 80 和 443 | VirtualNetwork | 儲存體 | |
| Intersubnet 通訊 | 輸入和輸出 | 80 和 443 | VirtualNetwork | VirtualNetwork | 子網路之間的通訊 |
| 對 Azure Logic Apps 的通訊 | 輸入 | 443 | 網際網路  | VirtualNetwork | 針對電腦或呼叫任何要求觸發程序或 webhook 存在於您的邏輯應用程式服務的 IP 位址。 關閉或封鎖此連接埠可避免使用要求觸發程序 logic apps 的 HTTP 呼叫。  |
| 執行歷程記錄的邏輯應用程式 | 輸入 | 443 | 網際網路  | VirtualNetwork | 您可以從此處檢視邏輯應用程式之電腦的 IP 位址的執行歷程記錄。 雖然關閉或封鎖此連接埠不會防止您檢視執行歷程記錄，您無法檢視輸入和輸出中的每個步驟的執行歷程記錄。 |
| 連線管理 | 輸出 | 443 | VirtualNetwork  | 網際網路 | |
| 發佈診斷記錄和計量 | 輸出 | 443 | VirtualNetwork  | AzureMonitor | |
| 從 Azure 流量管理員的通訊 | 輸入 | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps 設計工具 - 動態屬性 | 輸入 | 454 | 網際網路  | VirtualNetwork | 要求來自 Logic Apps[存取端點 IP 位址，該區域中的輸入](../logic-apps/logic-apps-limits-and-config.md#inbound)。 |
| App Service 管理相依性 | 輸入 | 454 和 455 | AppServiceManagement | VirtualNetwork | |
| 連接器部署 | 輸入 | 454 & 3443 | 網際網路  | VirtualNetwork | 需要部署及更新連接器。 關閉或封鎖此連接埠會導致失敗的 ISE 部署，並避免連接器更新或修正。 |
| Azure SQL 相依性 | 輸出 | 1433 | VirtualNetwork | SQL |
| Azure 資源健康狀態 | 輸出 | 1886 | VirtualNetwork | 網際網路 | 用於發佈至資源健康狀態的 健全狀況狀態 |
| API 管理 - 管理端點 | 輸入 | 3443 | APIManagement  | VirtualNetwork | |
| 「記錄到事件中樞」原則和監視代理程式的相依性 | 輸出 | 5672 | VirtualNetwork  | EventHub | |
| 針對角色執行個體之間的 Redis 執行個體存取 Azure 快取 | 輸入 <br>輸出 | 6379-6383 | VirtualNetwork  | VirtualNetwork | 此外，ISE 中使用 Azure 快取，redis，您必須開啟這些[Redis 常見問題集的 Azure 快取中所述的輸出和輸入連接埠](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
| Azure Load Balancer | 輸入 | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>建立您的 ISE

若要建立您的整合服務環境 (ISE)，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，於 Azure 主功能表上選取 [建立資源]。
在搜尋方塊中，輸入「整合服務環境」作為篩選條件。

   ![建立新資源](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在 [整合服務環境建立] 窗格中，選擇**建立**。

   ![選擇 [建立]](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 為您的環境提供這些詳細資料，然後選擇 [檢閱 + 建立]，例如：

   ![提供環境詳細資料](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 屬性 | 必要項 | Value | 說明 |
   |----------|----------|-------|-------------|
   | **訂用帳戶** | 有 | <*Azure-subscription-name*> | 要用於環境的 Azure 訂用帳戶 |
   | **資源群組** | 有 | <*Azure-resource-group-name*> | 您要用來建立環境的 Azure 資源群組 |
   | **整合服務環境名稱** | 有 | <*environment-name*> | 提供給環境的名稱 |
   | **位置** | 有 | <*Azure-datacenter-region*> | 要用來部署環境的 Azure 資料中心區域 |
   | **額外容量** | 有 | 0 到 10 | 若要使用此 ISE 資源的額外的處理單位數目。 若要在建立後增加容量，請參閱[新增 ISE 容量](#add-capacity)。 |
   | **虛擬網路** | 有 | <*Azure-virtual-network-name*> | 要插入環境的 Azure 虛擬網路，讓該環境中的邏輯應用程式可以存取虛擬網路。 如果您沒有網路，請[先建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。 <p>**重要**：您「只」可以在建立您的 ISE 時執行此插入作業。 |
   | **子網路** | 有 | <*subnet-resource-list*> | ISE 需要四個「空的」子網路，以在您的環境中建立資源。 若要建立每個子網路，[請遵循此表格底下的步驟](#create-subnet)。  |
   |||||

   <a name="create-subnet"></a>

   **建立子網路**

   若要建立您的環境中的資源，您的 ISE 需要四個*空*不委派給任何服務的子網路。 
   您*無法*建立您的環境之後，變更這些子網路位址。 每個子網路都必須符合下列準則：

   * 具有開頭為字母字元或底線，且不需要這些字元的名稱： `<`， `>`， `%`， `&`， `\\`， `?`， `/`

   * 會使用[無類別網域間路由選擇 (CIDR) 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和類別 B 位址空間。

   * 使用至少`/27`的位址空間，因為每個子網路都必須為 32 個位址*最小*。 例如：

     * `10.0.0.0/27` 有 32 個位址，因為 2<sup>(32-27)</sup>為 2<sup>5</sup>或 32。

     * `10.0.0.0/24` 有 256 個位址，因為 2<sup>(32-24)</sup>為 2<sup>8</sup>或 256。

     * `10.0.0.0/28` 具有只有 16 個位址，而且太小，因為 2<sup>(32-28)</sup>為 2<sup>4</sup>或 16。

     若要深入了解計算位址，請參閱[IPv4 CIDR 區塊](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果您使用[ExpressRoute](../expressroute/expressroute-introduction.md)，請記得[建立路由表](../virtual-network/manage-route-table.md)具有下列路由傳送，並將該資料表連結供您 ISE 每個子網路：

     **名稱**: <*路由名稱*><br>
     **位址前置詞**:0.0.0.0/0<br>
     **下一個躍點**：網際網路

   1. 在 [子網路] 清單底下，選擇 [管理子網路設定]。

      ![管理子網路設定](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. 在 [子網路] 窗格中，選擇 [子網路]。

      ![新增子網路](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. 在 [新增子網路] 窗格中，提供這項資訊。

      * **名稱**：您的子網路名稱
      * **位址範圍 (CIDR 區塊)**：在您虛擬網路中且使用 CIDR 格式的子網路範圍

      ![新增子網路詳細資料](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 完成時，選擇 [確定]。

   1. 針對其他三個子網路重複這些步驟。

      > [!NOTE]
      > 如果您嘗試建立的子網路不是有效的 Azure 入口網站會顯示一則訊息，但不會封鎖您的進度。

1. 在 Azure 成功驗證您的 ISE 資訊之後，選擇 [建立]，例如：

   ![驗證成功之後，選擇 [建立]](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 會開始部署您的環境，但此程序「可能」需要長達兩小時的時間才能完成。 
   若要檢查部署狀態，請在 Azure 工具列上選擇通知圖示，這會開啟 [通知] 窗格。

   ![檢查部署狀態](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   成功完成部署時，Azure 就會顯示此通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   否則，請依照疑難排解部署的 Azure 入口網站的指示。

   > [!NOTE]
   > 如果部署失敗，或刪除您 ISE 中，Azure 最多可能需要一小時之前釋放您的子網路。 此延遲表示您可能必須等候重複使用這些子網路，另一個的 ISE 中的表示。 
   >
   > 如果您刪除虛擬網路，Azure 通常需要兩個小時的時間之前釋放註冊您的子網路，但這項作業可能需要長時間。 
   > 當刪除的虛擬網路，請確定沒有任何資源仍然保持連線。 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 如果 Azure 在部署完成之後不會自動移至您的環境，且若要檢視您的環境，請選擇 [移至資源]。  

如需建立子網路的詳細資訊，請參閱[新增的虛擬網路子網路](../virtual-network/virtual-network-manage-subnet.md)。

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>建立邏輯應用程式 - ISE

若要建立在您的整合服務環境 (ISE) 中執行的邏輯應用程式[按照一般方式建立您的 logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)設定時，除了**位置**屬性，選取您從 ISE **整合服務環境**區段，例如：

  ![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

如何觸發程序和動作的工作和它們如何標示為 當您使用 ISE 相較於全域的邏輯應用程式服務，請參閱中的差異[隔離與全域 ISE 概觀中](connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>建立整合帳戶 - ISE

如果您想要使用 logic apps 的整合服務的環境 (ISE) 中的整合帳戶，必須使用該整合帳戶*同一個環境*與 logic apps。 ISE 中的邏輯應用程式只能參考相同 ISE 中的整合帳戶。

若要建立整合帳戶使用 ISE 中，[以一般方式建立您的整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)設定時，除了**位置**屬性中，選取您從 ISE**整合服務環境**區段，例如：

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>將 ISE 容量

ISE 基底單元有固定的容量，因此如果您需要更多的輸送量，您可以新增更多縮放單位。 您可以根據效能計量，或額外的處理單位數目為基礎的自動調整。 如果您選擇的計量為基礎的自動調整時，您可以選擇從各種不同的條件，並指定符合該準則的臨界值條件。

1. 在 Azure 入口網站中，找到您的 ISE。

1. 若要檢閱使用量和效能計量您 ISE 中，您的 ISE 主功能表上，選取**概觀**。

   ![Ise 檢視使用量](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. 若要設定自動調整，而下方**設定**，選取**相應放大**。在 **設定**索引標籤上，選擇**啟用自動調整規模**。

   ![開啟自動調整功能](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. 針對**自動調整規模設定名稱**，提供您設定的名稱。

1. 在 **預設**區段中，選擇 **依據計量調整規模**或**調整為特定執行個體計數**。

   * 如果您選擇執行個體為基礎，請輸入處理單位數介於 0 到 10 之間 （含）。

   * 如果您選擇計量為基礎，請遵循下列步驟：

     1. 在 **規則**區段中，選擇**新增規則**。

     1. 在 [**調整規模規則**] 窗格中，才會觸發此規則時設定的準則和動作。

     1. 當您完成時，選擇**新增**。

1. 當您完成時自動調整設定時，請儲存您的變更。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
