---
title: 範例-ISO 27001 ASE/SQL 工作負載藍圖-部署步驟
description: 部署步驟的 ISO 27001 應用程式服務環境/SQL Database 工作負載 blueprint （藍圖） 範例。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4ed51ee5f8cbdc50fa65a189d8f468bd7713a74b
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804151"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>部署 ISO 27001 應用程式服務環境/SQL Database 工作負載 blueprint （藍圖） 範例

若要部署 Azure 藍圖 ISO 27001 應用程式服務環境/SQL Database 工作負載 blueprint （藍圖） 範例，必須採取下列步驟：

> [!div class="checklist"]
> - 部署[ISO 27001 共用服務](../iso27001-shared/index.md)blueprint （藍圖） 範例
> - 從範例建立新的藍圖
> - 將您的範例複本標記為**已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free) 。

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>部署 ISO 27001 共用服務 blueprint （藍圖） 範例

在部署此藍圖範例之前， [ISO 27001 共用服務](../iso27001-shared/index.md)blueprint （藍圖） 的範例，必須部署到目標訂用帳戶。 沒有成功的 ISO 27001 共用服務藍圖範例部署，此藍圖範例將會遺失基礎結構相依性，而且在部署期間失敗。

> [!IMPORTANT]
> 此藍圖範例必須指派位於相同訂用帳戶[ISO 27001 共用服務](../iso27001-shared/index.md)blueprint （藍圖） 範例。

## <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，藉由以範例作為起始您環境中建立新的 blueprint （藍圖） 實作藍圖範例。

1. 選取 **所有的服務**並搜尋，然後選取**原則**的左窗格中。 在 **原則**頁面上，選取**藍圖**。

1. 在左側的 [快速入門] 頁面上，選取 [建立藍圖] **下方的 [建立]** 按鈕。

1. 尋找**ISO 27001:ASE/SQL 工作負載**blueprint （藍圖） 下的範例_其他樣本_，然後選取**使用此範例**。

1. 輸入藍圖範例的 [基本資料]：

   - **藍圖名稱**：提供的 ISO 27001 ASE/SQL 工作負載 blueprint （藍圖） 範例的名稱。
   - **定義位置**：使用省略符號並選取 管理群組，以儲存您的範例複本。

1. 在頁面頂端選取 [成品]索引標籤，或在頁面底部選取 **[下一步：成品]**。

1. 檢閱構成此藍圖範例的成品清單。 許多成品有更新版本，我們會定義的參數。 當您檢閱完藍圖範例時，請選取 [儲存草稿]。

## <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以**草稿**模式建立，而且必須先**發佈**，才能進行指派和部署。 Blueprint （藍圖） 範例的複本可以自訂您的環境和需求，但是這樣的修改可能會將它移開 ISO 27001 標準。

1. 選取 **所有的服務**並搜尋，然後選取**原則**的左窗格中。 在 **原則**頁面上，選取**藍圖**。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找您的藍圖範例複本，然後選取它。

1. 選取頁面頂端的 [發佈藍圖]。 在右側新頁面上，提供**版本**，您複製 blueprint （藍圖） 範例。 如果您稍後會進行修改，此屬性十分實用。 提供**變更備忘稿**例如 」 第一版發行從 ISO 27001 藍圖範例 」。 然後選取頁面底部的 [發佈]。

## <a name="assign-the-sample-copy"></a>指派範例複本

成功**發佈**藍圖範例複本後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖範例複本部署。

1. 選取 **所有的服務**並搜尋，然後選取**原則**的左窗格中。 在 **原則**頁面上，選取**藍圖**。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找您的藍圖範例複本，然後選取它。

1. 選取藍圖定義頁面頂端的 [指派藍圖]。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶**：在用來儲存藍圖範例複本的管理群組中，選取一或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱**：名稱是根據藍圖的名稱為您預先填入。
       視需要變更，或保持原狀。
     - **位置**：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **藍圖定義版本**：挑選**已發佈**版本複本的 blueprint （藍圖） 範例。

   - 鎖定指派

     選取設定您的環境的 blueprint （藍圖） 鎖定。 如需詳細資訊，請參閱[藍圖資源鎖定](../../concepts/resource-locking.md)。

   - 受控識別

     保留預設值_系統指派_受控身分識別選項。

   - 藍圖參數

     這一節中定義的參數供許多藍圖定義中的成品來提供一致性。

     - **組織名稱**:輸入您的組織的簡短名稱。 這個屬性主要用於命名資源。
     - **共用服務訂用帳戶識別碼**:訂用帳戶 ID 所在[ISO 27001 共用服務](../iso27001-shared/index.md)指派 blueprint （藍圖） 範例。
     - **預設子網路位址首碼**:虛擬網路的預設子網路的 CIDR 標記法。
       預設值是_10.1.0.0/16_。
     - **工作負載位置**:決定哪些成品部署到的位置。 並非所有可用服務的所有位置。 部署這類服務的成品提供要部署到該成品的位置的參數選項。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 如需完整清單或構件參數及其說明，請參閱[成品參數資料表](#artifact-parameters-table)。

1. 輸入所有參數後，選取頁面底部的 [指派]。 藍圖指派會建立並開始部署成品。 部署需要大約一小時的時間。 若要檢查部署的狀態，請開啟藍圖指派。

> [!WARNING]
> Azure 藍圖服務和內建的藍圖範例**免費的**。 Azure 資源[定價依產品](https://azure.microsoft.com/pricing/)。 使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估執行此範例中 blueprint （藍圖） 所部署資源的成本。

## <a name="artifact-parameters-table"></a>構件參數資料表

下表提供一份藍圖成品參數：

|成品名稱|成品類型|參數名稱|描述|
|-|-|-|-|
|Log Analytics 資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-workload-log-rg`以使資源群組是唯一。|
|Log Analytics 資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Log Analytics 範本|Resource Manager 範本|服務層|設定 Log Analytics 工作區的層。 預設值是_PerNode_。|
|Log Analytics 範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|Log Analytics 範本|Resource Manager 範本|位置|用來建立 Log Analytics 工作區的區域。 預設值是_美國西部 2_。|
|網路資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-workload-net-rg`以使資源群組是唯一。|
|網路資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|網路安全性群組範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|虛擬網路與路由表範本|Resource Manager 範本|Azure 防火牆私人 IP|設定的私人 IP [Azure 防火牆](../../../../firewall/overview.md)。 應該是 CIDR 標記法中所定義的一部分_ISO 27001:共用服務_成品參數**Azure 防火牆子網路位址首碼**。 預設值是_10.0.4.4_。|
|虛擬網路與路由表範本|Resource Manager 範本|共用服務訂用帳戶識別碼|用來啟用 VNET 對等互連的工作負載與共用服務之間的值。|
|虛擬網路與路由表範本|Resource Manager 範本|虛擬網路位址首碼|虛擬網路的 CIDR 標記法。 預設值是_10.1.0.0/16_。|
|虛擬網路與路由表範本|Resource Manager 範本|預設子網路位址首碼|虛擬網路的預設子網路的 CIDR 標記法。 預設值是_10.1.0.0/16_。|
|虛擬網路與路由表範本|Resource Manager 範本|加入 IP 位址|第一個加入 vm 的 IP 位址。 此值做為自訂的 VNET DNS。|
|金鑰保存庫資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-workload-kv-rg`以使資源群組是唯一。|
|金鑰保存庫資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Key Vault 範本|Resource Manager 範本|AAD 物件識別碼|需要 Key Vault 執行個體的存取帳戶的 AAD 物件識別碼。 沒有預設值，並不能空白。 若要尋找此值，從 Azure 入口網站，搜尋並選取 [使用者] 底下_Services_。 使用_名稱_方塊，以篩選的帳戶名稱並選取該帳戶。 在 _使用者設定檔_頁面上，選取 「 按一下以複製 」 圖示旁_物件識別碼_。|
|Key Vault 範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|Key Vault 範本|Resource Manager 範本|Key Vault SKU|指定的 SKU 所建立的金鑰保存庫。 預設值是_Premium_。|
|Key Vault 範本|Resource Manager 範本|Azure SQL Server 管理員使用者名稱|用來存取 Azure SQL Server 的使用者名稱。 必須符合相同的屬性值的**Azure SQL Database 範本**。 預設值是_sql 系統管理員使用者_。|
|Azure SQL Database 的資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-workload-azsql-rg`以使資源群組是唯一。|
|Azure SQL Database 的資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|Azure SQL Database 範本|Resource Manager 範本|Azure SQL Server 管理員使用者名稱|適用於 Azure 的 SQL Server 的使用者名稱。 必須符合相同的屬性值的**金鑰保存庫範本**。 預設值是_sql 系統管理員使用者_。|
|Azure SQL Database 範本|Resource Manager 範本|Azure SQL Server 系統管理員密碼 （金鑰保存庫資源識別碼）|金鑰保存庫的資源識別碼。 使用"/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv"並取代`{subscriptionId}`與您的訂用帳戶識別碼和`{orgName}`使用**組織名稱**藍圖參數。|
|Azure SQL Database 範本|Resource Manager 範本|Azure SQL Server 系統管理員密碼 （金鑰保存庫祕密名稱）|使用者名稱的 sql Server 系統管理員。必須符合中的值**金鑰保存庫範本**屬性**Azure SQL Server 系統管理員使用者名稱**。|
|Azure SQL Database 範本|Resource Manager 範本|記錄保留天數|資料保留期限天數。 預設值是_365_。|
|Azure SQL Database 範本|Resource Manager 範本|AAD 系統管理員的物件識別碼|AAD 物件識別碼的使用者，會指派為 Active Directory 系統管理員。沒有預設值，並不能空白。 若要尋找此值，從 Azure 入口網站，搜尋並選取 [使用者] 底下_Services_。 使用_名稱_方塊，以篩選的帳戶名稱並選取該帳戶。 在 _使用者設定檔_頁面上，選取 「 按一下以複製 」 圖示旁_物件識別碼_。|
|Azure SQL Database 範本|Resource Manager 範本|AAD 系統管理員登入|目前，Microsoft 帳戶 （例如 live.com 或 outlook.com） 不能設定為系統管理員。只有使用者和組織內的安全性群組可以設定為系統管理員。沒有預設值，並不能空白。 若要尋找此值，從 Azure 入口網站，搜尋並選取 [使用者] 底下_Services_。 使用_名稱_方塊，以篩選的帳戶名稱並選取該帳戶。 在 _使用者設定檔_頁面上，複製_使用者名_。|
|App Service 環境的資源群組|資源群組|名稱|**鎖住**-串連**組織名稱**使用`-workload-ase-rg`以使資源群組是唯一。|
|App Service 環境的資源群組|資源群組|位置|**鎖定**-使用藍圖參數。|
|App Service 環境範本|Resource Manager 範本|網域名稱|此範例所建立的 Active Directory 的名稱。 預設值是_contoso.com_。|
|App Service 環境範本|Resource Manager 範本|ASE 位置|App Service Environment 位置。 預設值是_美國西部 2_。|
|App Service 環境範本|Resource Manager 範本|應用程式閘道記錄保留天數|資料保留期限天數。 預設值是_365_。|

## <a name="next-steps"></a>後續步驟

既然您已檢閱部署 ISO 27001 應用程式服務環境/SQL Database 工作負載 blueprint （藍圖） 範例的步驟，請瀏覽下列文章以深入了解的架構和控制項對應：

> [!div class="nextstepaction"]
> [ISO 27001 應用程式服務環境/SQL Database 工作負載藍圖-概觀](./index.md)
> [ISO 27001 應用程式服務環境/SQL Database 工作負載藍圖-控制項對應](./control-mapping.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。