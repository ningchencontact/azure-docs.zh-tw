---
title: 範例-ISO 27001 共用服務藍圖-部署步驟
description: 部署步驟的 ISO 27001 共用服務 blueprint （藍圖） 範例。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7fea9b0d00d92c63bb4c8bbdadada9e8eb6a05fe
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202498"
---
# <a name="deploy-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>部署 Azure 藍圖 ISO 27001 共用的服務 blueprint （藍圖） 範例

若要部署 Azure 藍圖 ISO 27001 共用的服務 blueprint （藍圖） 範例，必須採取下列步驟：

> [!div class="checklist"]
> - 從範例建立新的藍圖
> - 標記的範例為您複本**已發佈**
> - 將您複製的藍圖指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free) 。

## <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，藉由以範例作為起始您環境中建立新的 blueprint （藍圖） 實作藍圖範例。

1. 選取 **所有的服務**並搜尋，然後選取**原則**的左窗格中。 在 **原則**頁面上，選取**藍圖**。

1. 從**快速入門**頁面上，於左側，選取**建立**按鈕下方_建立藍圖_。

1. 尋找**ISO 27001:共用服務**blueprint （藍圖） 下的範例_其他樣本_，然後選取**使用此範例**。

1. 請輸入_基本概念_的 blueprint （藍圖） 範例：

   - **藍圖名稱**:提供的 ISO 27001 共用服務 blueprint （藍圖） 範例的名稱。
   - **定義位置**:使用省略符號並選取 管理群組，以儲存您的範例複本。

1. 選取 _成品_頂端的頁面 索引標籤或**下一步:成品**在頁面底部。

1. 檢閱構成此藍圖範例的成品清單。 許多成品有更新版本，我們會定義的參數。 選取 **儲存草稿**當您完成檢閱 blueprint （藍圖） 範例。

## <a name="publish-the-sample-copy"></a>發行的樣本複製

您的藍圖範例複本現在已建立您的環境中。 它會建立在**草稿**模式，且必須**已發佈**指派和部署之前。 Blueprint （藍圖） 範例的複本可以自訂您的環境和需求，但是這樣的修改可能會將它移開 ISO 27001 標準。

1. 選取 **所有的服務**並搜尋，然後選取**原則**的左窗格中。 在 **原則**頁面上，選取**藍圖**。

1. 選取 **藍圖定義**左邊的頁面。 使用篩選來尋找您的藍圖範例複本，然後選取它。

1. 選取 **發佈藍圖**頁面的頂端。 在右側新頁面上，提供**版本**，您複製 blueprint （藍圖） 範例。 這個屬性可用於快，如果您稍後修改。 提供**變更備忘稿**例如 」 第一版發行從 ISO 27001 藍圖範例 」。 然後選取**發佈**在頁面底部。

## <a name="assign-the-sample-copy"></a>將指定的樣本複製

一旦 blueprint （藍圖） 範例的複本已順利**已發佈**，可以將它指派給它已儲存至管理群組中的訂用帳戶。 未提供參數以使每個部署的 blueprint （藍圖） 範例的複本是唯一時，則此步驟。

1. 選取 **所有的服務**並搜尋，然後選取**原則**的左窗格中。 在 **原則**頁面上，選取**藍圖**。

1. 選取 **藍圖定義**左邊的頁面。 使用篩選來尋找您的藍圖範例複本，然後選取它。

1. 選取 **指派藍圖**blueprint （藍圖） 的 定義 頁面頂端。

1. 提供的參數值的藍圖指派：

   - 基本概念

     - **訂用帳戶**：選取一或多個訂用帳戶，您會在管理群組中儲存的 blueprint （藍圖） 範例。 如果您選取多個訂用帳戶時，會建立指派，為每個輸入的參數。
     - **指派名稱**:名稱是根據藍圖的名稱為您預先填入。
       視需要變更，或保持原狀。
     - **位置**：選取受管理的身分識別，在中建立的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **藍圖定義版本**:挑選**已發佈**版本複本的 blueprint （藍圖） 範例。

   - 鎖定指派

     選取設定您的環境的 blueprint （藍圖） 鎖定。 如需詳細資訊，請參閱[藍圖資源鎖定](../../concepts/resource-locking.md)。

   - 受控識別

     保留預設值_系統指派_受控身分識別選項。

   - 藍圖參數

     這一節中定義的參數供許多藍圖定義中的成品來提供一致性。

     - **組織名稱**:輸入您的組織的簡短名稱。 這個屬性主要用於命名資源。
     - **共用的服務子網路位址首碼**:提供的 CIDR 標記法值一起網路所部署的資源。
     - **共用服務位置**:決定哪些成品部署到的位置。 並非所有可用服務的所有位置。 部署這類服務的成品提供要部署到該成品的位置的參數選項。
     - **允許的位置 (原則：Iso 27001 藍圖 initiative）**:值，指出允許的位置，針對資源群組和資源。
     - **Log Analytics 工作區的 VM 代理程式 (原則：Iso 27001 藍圖 initiative）**:指定工作區的資源識別碼。 這個參數會使用`concat`函式來建構資源識別碼。

   - 構件參數

     在本節中所定義的參數套用至其定義所在的成品。 這些參數是[動態參數](../../concepts/parameters.md#dynamic-parameters)因為定義期間的藍圖指派。 如需完整清單或構件參數及其說明，請參閱[成品參數資料表](#artifact-parameters-table)。

1. 一旦輸入所有參數後，選取**指派**在頁面底部。 藍圖指派會建立並開始部署成品。 部署需要大約一小時的時間。 若要檢查部署的狀態，請開啟藍圖指派。

> [!WARNING]
> Azure 藍圖服務和內建的藍圖範例**免費的**。 Azure 資源[定價依產品](https://azure.microsoft.com/en-us/pricing/)。 使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估執行此範例中 blueprint （藍圖） 所部署資源的成本。

## <a name="artifact-parameters-table"></a>構件參數資料表

下表提供一份藍圖成品參數：

|成品名稱|成品類型|參數名稱|描述|
|-|-|-|-|
|[預覽]：部署 Linux VM 擴展集 (VMSS) 的 Log Analytics 代理程式|原則指派|選用：具有支援新增至範圍的 Linux OS 的 VM 映像清單|（選擇性）預設值是 _["none"]_。|
|[預覽]：部署適用於 Linux VM 的 Log Analytics 代理程式|原則指派|選用：具有支援新增至範圍的 Linux OS 的 VM 映像清單|（選擇性）預設值是 _["none"]_。|
|[預覽]：部署 Windows VM 擴展集 (VMSS) 的 Log Analytics 代理程式|原則指派|選用：具有支援新增至範圍的 Windows OS 的 VM 映像清單|（選擇性）預設值是 _["none"]_。|
|[預覽]：部署適用於 Windows VM 的 Log Analytics 代理程式|原則指派|選用：具有支援新增至範圍的 Windows OS 的 VM 映像清單|（選擇性）預設值是 _["none"]_。|
|允許的資源類型|原則指派|允許的資源類型|允許部署的資源類型的清單。 這份清單是由共用服務中所部署的所有資源類型所都組成。|
|允許的儲存體帳戶 SKU|原則指派|允許的儲存體 Sku|清單中的診斷記錄儲存體帳戶 Sku 允許的。 預設值是 _["Standard_LRS"]_。|
|允許的虛擬機器 SKU|原則指派|允許部署虛擬機器 Sku 的清單。 預設值是 _["Standard_DS1_v2"，"Standard_DS2_v2"]_。|
|ISO 27001 的藍圖方案|原則指派|稽核診斷記錄的資源類型|若要稽核是否未啟用診斷記錄設定的資源類型的清單。 可接受的值，請參閱[Azure 監視器診斷記錄結構描述](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)。|
|Log Analytics 資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-sharedsvsc-log-rg`以使資源群組是唯一。|
|Log Analytics 資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Log Analytics 範本|Resource Manager 範本|服務層|設定 Log Analytics 工作區的層。 預設值是_PerNode_。|
|Log Analytics 範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|Log Analytics 範本|Resource Manager 範本|位置|用來建立 Log Analytics 工作區的區域。 預設值是_美國西部 2_。|
|網路資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-sharedsvcs-net-rg`以使資源群組是唯一。|
|網路資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Azure 防火牆範本|Resource Manager 範本|Azure 防火牆私人 IP|設定的私人 IP [Azure 防火牆](../../../../firewall/overview.md)。 此值也會為共用的服務子網路上的預設路由表。 應該是 CIDR 標記法中所定義的一部分**Azure 防火牆子網路位址首碼**。 預設值是_10.0.4.4_。|
|Azure 防火牆範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|網路安全性群組範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|虛擬網路與路由表範本|Resource Manager 範本|虛擬網路位址首碼|虛擬網路的 CIDR 標記法。 預設值是_10.0.0.0/16_。|
|虛擬網路與路由表範本|Resource Manager 範本|啟用虛擬網路 DDoS 保護|設定虛擬網路的 DDoS 保護。 預設值是 _，則為 true_。|
|虛擬網路與路由表範本|Resource Manager 範本|共用服務子網路位址首碼|共用服務子網路的 CIDR 標記法。 預設值是_10.0.0.0/24_。|
|虛擬網路與路由表範本|Resource Manager 範本|DMZ 子網路位址首碼|DMZ 子網路的 CIDR 標記法。 預設值是_10.0.1.0/24_。|
|虛擬網路與路由表範本|Resource Manager 範本|應用程式閘道子網路位址首碼|應用程式閘道子網路的 CIDR 標記法。 預設值是_10.0.2.0/24_。|
|虛擬網路與路由表範本|Resource Manager 範本|虛擬網路閘道子網路位址首碼|虛擬網路閘道子網路的 CIDR 標記法。 預設值是_10.0.3.0/24_。|
|虛擬網路與路由表範本|Resource Manager 範本|Azure 防火牆子網路位址首碼|CIDR 標記法[Azure 防火牆](../../../../firewall/overview.md)子網路。 應該包含**Azure 防火牆的私人 IP**參數。|
|金鑰保存庫資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-sharedsvcs-kv-rg`以使資源群組是唯一。|
|金鑰保存庫資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Key Vault 範本|Resource Manager 範本|Jumpbox 系統管理員使用者名稱|針對 jumpbox 的使用者名稱。 必須符合相同的屬性值的**Jumpbox 範本**。 預設值是_jb 系統管理員使用者_。|
|Key Vault 範本|Resource Manager 範本|Jumpbox 系統管理員的 SSH 金鑰或密碼|索引鍵或 jumpbox 之帳戶的密碼。 必須符合相同的屬性值的**Jumpbox 範本**。 沒有預設值，並不能空白。|
|Key Vault 範本|Resource Manager 範本|網域系統管理員使用者名稱|用來存取 Active Directory VM，並將其他 Vm 加入網域的使用者名稱。 必須符合**網域系統管理員使用者**中的屬性值**Active Directory 網域服務範本**。 預設值是_網域系統管理員使用者_。|
|Key Vault 範本|Resource Manager 範本|網域系統管理員密碼|網域系統管理員使用者的密碼。 沒有預設值，並不能空白。|
|Key Vault 範本|Resource Manager 範本|AAD 物件識別碼|需要 Key Vault 執行個體的存取帳戶的 AAD 物件識別碼。 沒有預設值，並不能空白。 若要尋找此值，從 Azure 入口網站，搜尋並選取 [使用者] 底下_Services_。 使用_名稱_方塊，以篩選的帳戶名稱並選取該帳戶。 在 _使用者設定檔_頁面上，選取 「 按一下以複製 」 圖示旁_物件識別碼_。  |
|Key Vault 範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|Key Vault 範本|Resource Manager 範本|Key Vault SKU|指定的 SKU 所建立的金鑰保存庫。 預設值是_Premium_。|
|Jumpbox 的資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-sharedsvcs-jb-rg`以使資源群組是唯一。|
|Jumpbox 的資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 系統管理員使用者名稱|用來存取 jumpbox Vm 的使用者名稱。 必須符合相同的屬性值的**金鑰保存庫範本**。 預設值是_jb 系統管理員使用者_。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 系統管理員密碼 （金鑰保存庫資源識別碼）|金鑰保存庫的資源識別碼。 使用"/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"並取代`{subscriptionId}`與您的訂用帳戶識別碼和`{orgName}`使用**組織名稱**藍圖參數。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 系統管理員密碼 （金鑰保存庫祕密名稱）|Jumpbox 管理員的使用者名稱必須符合中的值**金鑰保存庫範本**屬性**Jumpbox 系統管理員使用者名稱**。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 作業系統|判斷系統的 jumpbox VM 的作業系統。 預設值是_Windows_。|
|Active Directory 網域服務的資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-sharedsvcs-adds-rg`以使資源群組是唯一。|
|Active Directory 網域服務的資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域系統管理員使用者名稱|ADDS jumpbox 的使用者名稱。 必須符合相同的屬性值的**金鑰保存庫範本**。 預設值是_新增系統管理員使用者_。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域系統管理員密碼 （金鑰保存庫資源識別碼）|金鑰保存庫的資源識別碼。 使用"/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"並取代`{subscriptionId}`與您的訂用帳戶識別碼和`{orgName}`使用**組織名稱**藍圖參數。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域系統管理員密碼 （金鑰保存庫祕密名稱）|使用者名稱的網域系統管理員。必須符合中的值**金鑰保存庫範本**屬性**網域系統管理員使用者名稱**。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域名稱|此範例所建立的 Active Directory 的名稱。 預設值是_contoso.com_。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域系統管理員使用者|管理 AD 帳戶，以及將裝置加入 AD 網域的使用者名稱。 必須符合**AD 系統管理員使用者名稱**中的屬性值**金鑰保存庫範本**。 預設值是_網域系統管理員使用者_。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域系統管理員密碼|設定金鑰保存庫詳細資料，來儲存密碼。 沒有預設值，並不能空白。|

## <a name="next-steps"></a>後續步驟

既然您已檢閱部署 ISO 27001 共用服務 blueprint （藍圖） 範例的步驟，請瀏覽下列文章以深入了解的架構和控制項對應：

> [!div class="nextstepaction"]
> [ISO 27001 共用服務藍圖-概觀](./index.md)
> [ISO 27001 共用服務藍圖-控制項對應](./control-mapping.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。