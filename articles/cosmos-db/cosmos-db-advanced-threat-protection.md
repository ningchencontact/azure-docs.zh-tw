---
title: Azure Cosmos DB 的先進威脅防護
description: 瞭解 Azure Cosmos DB 如何提供待用資料的加密，以及它的實作為方式。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: dca653bfaf625ff31e4dd7f43752219b312779d1
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286773"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB 的先進威脅防護

Azure Cosmos DB 的先進威脅防護提供一層額外的安全性情報，可偵測不尋常且可能有害的嘗試存取或惡意探索 Azure Cosmos DB 帳戶。 這一層保護可讓您處理威脅，即使不是安全性專家，也能將它們與中央安全性監視系統整合。

當活動發生異常時，就會觸發安全性警示。 這些安全性警示會與[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合，並透過電子郵件傳送給訂用帳戶系統管理員，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。

> [!NOTE]
>
> * Azure Cosmos DB 的先進威脅防護目前僅適用于 SQL API。
> * Azure Cosmos DB 的先進威脅防護目前無法在 Azure 政府和主權雲端區域中使用。

如需安全性警示的完整調查體驗，建議您[在 Azure Cosmos DB 中啟用診斷記錄](https://docs.microsoft.com/azure/cosmos-db/logging)，這會記錄資料庫本身的作業，包括對所有檔、容器和資料庫的 CRUD 作業。

## <a name="set-up-advanced-threat-protection"></a>設定先進的威脅防護

### <a name="set-up-atp-using-the-portal"></a>使用入口網站設定 ATP

1. 啟動[https://portal.azure.com](https://portal.azure.com/)的 Azure 入口網站。

2. 從 [Azure Cosmos DB] 帳戶的 [**設定**] 功能表中，選取 [ **Advanced security**]。

    ![設定 ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. 在 [ **Advanced security** configuration] 分頁中：

    * 按一下 [ **Advanced 威脅防護**] 選項，將其設定為 [**開啟**]。
    * 按一下 [儲存]，以儲存新的或更新的進階威脅防護原則。   

### <a name="set-up-atp-using-rest-api"></a>使用 REST API 設定 ATP

使用 Rest API 命令來建立、更新或取得特定 Azure Cosmos DB 帳戶的「先進的威脅防護」設定。

* [先進的威脅防護-建立](https://go.microsoft.com/fwlink/?linkid=2099745)
* [先進的威脅防護-取得](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>使用 Azure PowerShell 設定 ATP

使用下列 PowerShell Cmdlet：

* [啟用先進的威脅防護](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [取得先進的威脅防護](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [停用先進的威脅防護](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

使用 Azure Resource Manager 範本來設定已啟用「先進的威脅防護」的 Cosmos DB。
如需詳細資訊，請參閱[使用 Advanced 威脅防護建立 CosmosDB 帳戶](https://azure.microsoft.com/en-us/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)。

### <a name="using-azure-policy"></a>使用 Azure 原則

使用 Azure 原則，為 Cosmos DB 啟用先進的威脅防護。

1. 啟動 [Azure**原則-定義**] 頁面，然後搜尋 Cosmos DB 原則的 [**部署 Advanced 威脅防護**]。

    ![搜尋原則](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. 按一下 [**為 CosmosDB 部署先進的威脅防護**] 原則，然後按一下 [**指派**]。

    ![選取訂用帳戶或群組](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. 在 [**範圍**] 欄位中，按一下三個點，選取 [Azure 訂用帳戶] 或 [資源群組]，然後按一下 [**選取**]。

    ![原則定義頁面](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. 輸入其他參數，然後按一下 [**指派**]。

## <a name="manage-atp-security-alerts"></a>管理 ATP 安全性警示

當 Azure Cosmos DB 的活動異常發生時，會觸發安全性警示，並提供可疑安全性事件的相關資訊。 

 從 Azure 資訊安全中心，您可以審查和管理目前的[安全性警示](../security-center/security-center-alerts-overview.md)。  按一下[資訊安全中心](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0)中的特定警示，以查看可能的原因和建議的動作，以調查並減輕潛在威脅。 下圖顯示資訊安全中心中提供的警示詳細資料範例。

 ![威脅詳細資料](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

系統也會傳送電子郵件通知，其中包含警示詳細資料和建議的動作。 下圖顯示警示電子郵件的範例。

 ![警示詳細資料](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP 警示

 若要查看監視 Azure Cosmos DB 帳戶時所產生的警示清單，請參閱資訊安全中心檔中的[Cosmos DB 警示](../security-center/security-center-alerts-data-services.md#cosmos-db)一節。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure Cosmos DB 中的診斷記錄](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
