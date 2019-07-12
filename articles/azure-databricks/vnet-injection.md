---
title: 在您的虛擬網路 （預覽） 中部署 Azure Databricks
description: 本文說明如何將 Azure Databricks 部署至您的虛擬網路，也就是 VNet 插入式攻擊。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126676"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>在您的虛擬網路 （預覽） 中部署 Azure Databricks

Azure Databricks 的預設部署為 Azure 上完全受控的服務： 所有的資料平面資源，包括虛擬網路 (VNet)，會部署到鎖定的資源群組。 如果您需要自訂網路，不過，您可以部署 Azure Databricks 資源在您自己的虛擬網路 （也稱為 VNet 資料隱碼攻擊），當可讓您：

* Azure Databricks 連線至其他 Azure 服務 （例如 Azure 儲存體），以更安全的方式使用服務端點。
* 連接到內部部署資料來源使用與 Azure Databricks，利用使用者定義的路由。
* Azure Databricks 連線至網路虛擬設備來檢查所有輸出流量，並採取動作，以根據允許和拒絕規則。
* 設定為使用自訂 DNS 的 Azure Databricks。
* 設定網路安全性群組 (nsg) 規則來指定輸出資料傳輸限制。
* 部署您現有的虛擬網路中的 Azure Databricks 叢集。

將 Azure Databricks 資源部署至您自己的虛擬網路也可讓您充分利用彈性的 CIDR 範圍 (/16-之間的任何位置 / 24 的虛擬網路及之間 /18-/ 26 的子網路)。

  > [!NOTE]
  > 您無法取代現有的工作區的虛擬網路。 若您目前的工作區無法容納所需的作用中叢集節點數目，請在較大的虛擬網路中建立另一個工作區。 請遵循[這些詳細的移轉步驟](howto-regional-disaster-recovery.md#detailed-migration-steps)將從舊的資源 （notebook、 叢集設定、 工作） 複製到新的工作區。

## <a name="virtual-network-requirements"></a>虛擬網路需求

您可以使用 Azure Databricks 工作區部署介面，在 Azure 入口網站中的現有的虛擬網路自動設定必要的子網路、 網路安全性群組和列入允許清單設定，或者您可以使用 Azure Resource Manager若要設定您的虛擬網路及部署您的工作區的範本。

部署至 Azure Databricks 工作區的虛擬網路必須符合下列需求：

### <a name="location"></a>Location

虛擬網路必須位於與 Azure Databricks 工作區相同的位置。

### <a name="subnets"></a>子網路

虛擬網路必須包含兩個專用於 Azure Databricks 的子網路：

   1. 私人子網路與設定的網路安全性群組，可讓叢集內部通訊

   2. 公用子網路與設定的網路安全性群組可與 Azure Databricks 控制平面之間的通訊。

### <a name="address-space"></a>位址空間

/ 16/24 的虛擬網路之間 /18/26 的私人和公用子網路的 CIDR 區塊的 CIDR 區塊。

### <a name="whitelisting"></a>加入允許清單

所有的輸出和輸入流量的子網路與 Azure Databricks 控制平面之間必須列入允許清單。

## <a name="create-an-azure-databricks-workspace"></a>建立 Azure Databricks 工作區

本節說明如何在 Azure 入口網站中建立 Azure Databricks 工作區，並將它部署在您自己現有的虛擬網路中。 Azure Databricks 會更新虛擬網路有兩個新的子網路和網路安全性群組，使用您所提供的 CIDR 範圍時，允許清單的輸入和輸出的子網路的流量，並將工作區部署到已更新的虛擬網路。

## <a name="prerequisites"></a>先決條件

您必須擁有要用來將部署在 Azure Databricks 工作區的虛擬網路。 您可以使用現有的虛擬網路，或建立新的連線，但虛擬網路必須位於與您打算建立 Azure Databricks 工作區相同的區域。 需要虛擬網路之間/16/24 的 CIDR 範圍。

  > [!Warning]
  > 工作區，以較小的虛擬網路 – 也就是較低的 CIDR 範圍 – 可以用完 IP 位址 （網路空間） 速度比將工作區和更大的虛擬網路。 比方說，/24 的工作區的虛擬網路和/26 子網路可以有最多 64 個節點作用中一次，而 /20 的工作區的虛擬網路和/22 部子網路可包含最多 1024年節點。

  當您設定您的工作區中，以及您將有機會在設定期間提供的子網路的 CIDR 範圍，將自動建立您的子網路。

## <a name="configure-the-virtual-network"></a>設定虛擬網路

1. 在 Azure 入口網站中，選取 **+ 建立資源 > 分析 > Azure Databricks**以開啟 [Azure Databricks 服務] 對話方塊。

2. 請依照下列步驟 2 中所述的設定步驟：建立 Azure Databricks 工作區中快速入門指南，並部署 Azure Databricks 工作區中選取您虛擬網路的選項。

   ![建立 Azure Databricks 服務](./media/vnet-injection/create-databricks-service.png)

3. 選取您想要使用的虛擬網路。

   ![虛擬網路的選項](./media/vnet-injection/select-vnet.png)

4. 提供的兩個子網路，Azure databricks 專用 /18/26 之間的區塊中的 CIDR 範圍：

   * 透過相關聯的網路安全性群組可與 Azure Databricks 控制平面之間的通訊，將會建立公用子網路。
   * 私人子網路將會建立透過相關聯的網路安全性群組，可讓叢集內部通訊。

5. 按一下 **建立**部署至虛擬網路的 Azure Databricks 工作區。

## <a name="advanced-resource-manager-configurations"></a>進階的資源管理員組態

如果您想要更充分掌控的虛擬網路組態 – 比方說，您要使用現有的子網路、 使用現有網路安全性群組，或建立您自己的安全性規則 – 您可以使用下列的 Azure Resource Manager 範本，而不是入口網站的虛擬網路組態和工作區部署。

### <a name="all-in-one"></a>全都放在其中一個

全都放在其中建立虛擬網路、 網路安全性群組和 Azure Databricks 工作區，請使用[Databricks VNet 插入工作區-全方位範本](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)。

當您使用此範本時，您不需要執行任何手動加入白名單的子網路流量。

### <a name="network-security-groups"></a>網路安全性群組

若要建立網路安全性群組與現有的虛擬網路的必要規則，請使用[Databricks VNet 資料隱碼攻擊的網路安全性群組範本](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection)。

當您使用此範本時，您不需要執行任何手動加入白名單的子網路流量。

### <a name="virtual-network"></a>虛擬網路

若要建立適當的公用和私人子網路的虛擬網路，使用[Databricks VNet 資料隱碼攻擊的虛擬網路範本](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection)。

如果您使用此範本也不用網路安全群組範本時，您必須手動加入白名單規則您使用與虛擬網路的網路安全性群組。

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作區

若要將 Azure Databricks 工作區部署到現有的虛擬網路具有公用和私人子網路和已設定的設定正確的網路安全性群組中，使用[Databricks VNet 資料隱碼的工作區範本](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection)。

如果您使用此範本也不用網路安全群組範本時，您必須手動加入白名單規則您使用與虛擬網路的網路安全性群組。

## <a name="whitelisting-subnet-traffic"></a>加入白名單的子網路流量

如果您不要使用[Azure 入口網站](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal)或是[Azure Resource Manager 範本](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced)若要建立您的網路安全性群組，您必須手動加入白名單下列流量子網路上。

|Direction|Protocol|Source|來源連接埠|目的地|目的地連接埠|
|---------|--------|------|-----------|-----------|----------------|
|輸入|\*|VirtualNetwork|\*|\*|\*|
|輸入|\*|控制平面 NAT IP|\*|\*|22|
|輸入|\*|控制平面 NAT IP|\*|\*|5557|
|輸出|\*|\*|\*|Webapp IP|\*|
|輸出|\*|\*|\*|SQL （服務索引標籤）|\*|
|輸出|\*|\*|\*|儲存體 （服務索引標籤）|\*|
|輸出|\*|\*|\*|VirtualNetwork|\*|

列入白名單的子網路流量，使用下列的 IP 位址。 SQL （中繼存放區） 和儲存體 （成品和記錄儲存體），您應該使用 Sql 和儲存體[服務標籤](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

|Azure Databricks 區域|服務|公用 IP|
|-----------------------|-------|---------|
|East US|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美國東部 2|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美國中北部|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美國中部|控制平面 NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|美國中南部|控制平面 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|美國西部|控制平面 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|美國西部 2|控制平面 NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|加拿大中部|控制平面 NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|加拿大東部|控制平面 NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|英國西部|控制平面 NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|英國南部|控制平面 NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|西歐|控制平面 NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|北歐|控制平面 NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|印度中部|控制平面 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|印度南部|控制平面 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|印度西部|控制平面 NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|東南亞|控制平面 NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|東亞|控制平面 NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|澳洲東部|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亞東南部|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亞中部|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|澳大利亞中部 2|控制平面 NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|日本東部|控制平面 NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|日本西部|控制平面 NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>疑難排解

### <a name="workspace-launch-errors"></a>工作區啟動錯誤

啟動自訂的虛擬網路中的工作區失敗，在 Azure Databricks 上登入畫面，發生下列錯誤： **「 我們已發現建立您的工作區時發生錯誤。請確定自訂網路設定正確並再試一次 」。**

此錯誤被因不符合需求的網路組態。 請確認您遵循本主題中的指示，當您建立工作區。

### <a name="cluster-creation-errors"></a>叢集建立錯誤

**無法連線到的執行個體：資源不是可透過 SSH 連線。**

可能的原因： 背景工作角色從控制平面流量遭到封鎖。 修正藉由確定 輸入的安全性規則符合需求。 如果您要部署到現有的虛擬網路連線到內部部署網路，請檢閱您的設定使用中連線到內部部署網路的 Azure Databricks 工作區所提供的資訊。

**未預期的啟動失敗：設定叢集時發生未預期的錯誤。重試，如果問題持續發生，請連絡 Azure Databricks。內部錯誤訊息：下節點時的逾時。**

可能的原因： 背景工作角色從 Azure 儲存體端點的流量遭到封鎖。 修正確保輸出安全性規則符合需求。 如果您使用自訂 DNS 伺服器，也請檢查您的虛擬網路中的 DNS 伺服器的狀態。

**雲端提供者啟動失敗：設定叢集時發生雲端提供者錯誤。請參閱 Azure Databricks 指南以取得詳細資訊。Azure 錯誤碼：AuthorizationFailed/InvalidResourceReference.**

可能的原因： 虛擬網路或子網路不再存在。 請確定虛擬網路和子網路存在。

**終止叢集。原因：Spark 啟動失敗：Spark 程式無法啟動的時間。這個問題可能造成故障的 Hive 中繼存放區、 無效的 Spark 組態或運作失常的 init 指令碼。Spark 驅動程式記錄檔，以疑難排解此問題，請參閱，如果問題持續發生，請連絡 Databricks。內部錯誤訊息：Spark 無法啟動：驅動程式無法啟動的時間。**

可能的原因：容器無法連絡主控執行個體或 DBFS 儲存體帳戶。 修正方法是加入 DBFS 儲存體帳戶的子網路中的自訂路由，與正在網際網路的下一個躍點。

### <a name="notebook-command-errors"></a>Notebook 命令發生錯誤

**命令沒有回應**

可能的原因： 背景工作-背景工作通訊遭到封鎖。 修正藉由確定輸入的安全性規則符合需求。

**Notebook 工作流程失敗，發生例外狀況： com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

可能的原因： 從背景工作角色到 Azure Databricks Webapp 流量遭到封鎖。 修正藉由確認 輸出安全性規則符合需求。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure Databrick 擷取、轉換和載入資料](databricks-extract-load-sql-data-warehouse.md)
