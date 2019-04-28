---
title: 將現有的 Azure 服務匯流排標準命名空間移轉到 「 進階 」 層 |Microsoft Docs
description: 若要移轉現有的 Azure 服務匯流排標準命名空間，premium 的指南
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: d2aa018566695b5b94cd8a7e64931a8b776b151d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766306"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>將現有的 Azure 服務匯流排標準命名空間移轉到 「 進階 」 層
先前，Azure 服務匯流排提供只能在標準層上的命名空間。 命名空間是最適合用於低的輸送量和開發人員環境的多租用戶設定。 進階層提供每個命名空間的可預測的延遲並增加的輸送量，以固定價格的專用的資源。 進階層已針對高輸送量和所需的其他企業功能的實際執行環境最佳化。

本文說明如何將現有的標準層命名空間移轉至 「 進階 」 層。

>[!WARNING]
> 移轉適用於升級為優質層次的服務匯流排標準命名空間。 移轉工具不支援降級。

某些幾點需要注意： 
- 此移轉要發生的位置，這表示現有的傳送者與接收者應用程式**不需要任何程式碼或組態變更**。 現有的連接字串會自動指向新的進階命名空間。
- **Premium**命名空間應該**任何實體**中進行移轉失敗。 
- 所有**實體**標準命名空間中是**複製**為進階命名空間，在移轉過程。 
- 移轉支援**1,000 個實體，每個傳訊單位**進階層。 若要識別您需要多少個傳訊單位，開始您對您目前的標準命名空間的實體數目。 

## <a name="migration-steps"></a>移轉步驟
某些條件是移轉程序相關聯。 熟悉下列步驟，以減少發生錯誤的可能性。 下列步驟概述的移轉程序，並在下列各節中列出的逐步詳細資料。

1. 建立新的進階命名空間。
1. 配對彼此的標準和進階命名空間。
1. 將實體從標準同步處理 （透過複製） 為進階命名空間。
1. 認可移轉。
1. 使用命名空間的移轉後名稱清空標準命名空間中的實體。
1. 刪除標準命名空間。

>[!IMPORTANT]
> 移轉已認可之後，存取舊的標準命名空間，然後清空佇列和訂用帳戶。 訊息已清空後，它們可能會傳送至新的進階命名空間由接收者應用程式來處理。 清空佇列和訂用帳戶之後，我們建議您刪除舊的標準命名空間。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 來移轉

若要使用 Azure CLI 或 PowerShell 工具，您的服務匯流排標準命名空間移轉至進階，請遵循下列步驟。

1. 建立新的服務匯流排進階命名空間。 您可以參考[Azure Resource Manager 範本](service-bus-resource-manager-namespace.md)或是[使用 Azure 入口網站](service-bus-create-namespace-portal.md)。 請務必選取**premium** for **serviceBusSku**參數。

1. 設定下列環境變數來簡化移轉命令。
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 移轉後名稱 (post_migration_dns_name) 將用於存取舊的標準命名空間移轉後。 使用此選項可清空佇列和訂用帳戶，然後再刪除 命名空間。

1. 配對的標準和進階命名空間，然後使用下列命令啟動同步處理：

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. 使用下列命令，以檢查移轉的狀態：
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    移轉會被視為完成，當您看到下列值：
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    此命令也會顯示移轉設定。 檢查以確定已正確設定的值。 也請檢查進階命名空間，在入口網站中，以確保所有佇列和主題都建立之後，且其符合標準的命名空間中的狀態。

1. 藉由執行下列的完整命令認可移轉：
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>使用 Azure 入口網站移轉

使用 Azure 入口網站移轉具有相同的邏輯流程，做為移轉所使用的命令。 請遵循下列步驟以使用 Azure 入口網站移轉。

1. 在 **瀏覽**的左窗格中，選取功能表**移轉至進階**。 按一下 **開始**按鈕以繼續前往下一個頁面。
    ![移轉登陸頁面][]

1. 完整**安裝程式**。
   ![設定命名空間][]
   1. 建立並指派進階命名空間移轉到現有的標準命名空間。
        ![設定命名空間-建立進階命名空間][]
   1. 選擇**移轉名稱**。 若要存取標準命名空間，在移轉完成之後，您將使用此名稱。
        ![設定命名空間-挑選 post 移轉名稱][]
   1. 選取  **'Next'** 以繼續。
1. 標準和進階命名空間之間的同步處理實體。
    ![設定命名空間-同步處理實體-開始][]

   1. 選取 **開始同步處理**開始同步處理的實體。
   1. 選取 **是**在對話方塊中，確認並開始同步處理。
   1. 等待直到完成同步為止。 在 [狀態] 列上使用狀態。
        ![設定命名空間-同步處理實體-進度][]
        >[!IMPORTANT]
        > 如果您需要中止移轉，因為任何原因，請檢閱這份文件常見問題集一節中的中止流程。
   1. 在同步處理完成之後，請選取**下一步**在頁面底部。

1. 檢閱 [摘要] 頁面上的變更。 選取 **完成移轉**切換命名空間，並完成移轉。
    ![切換命名空間-切換功能表][]完成移轉時，會出現 [確認] 頁面。
    ![參數命名空間-成功][]

## <a name="faqs"></a>常見問題集

### <a name="what-happens-when-the-migration-is-committed"></a>認可移轉時，會發生什麼事？

認可移轉後，指向標準命名空間的連接字串會指向進階命名空間中。

寄件者與接收者應用程式會中斷與標準的命名空間的連線，並自動重新連線到進階命名空間。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>該怎麼辦標準版至進階移轉完成之後？

標準版至進階移轉可確保例如主題、 訂用帳戶和篩選的實體中繼資料複製到進階命名空間標準命名空間。 從標準的命名空間不複製標準命名空間所認可的訊息資料，為進階命名空間。

標準的命名空間可能會有某些訊息已傳送及認可時移轉正在進行中。 手動清空 標準命名空間中的這些訊息，並以手動方式將它們傳送至進階命名空間。 若要手動清空的訊息，使用主控台應用程式或使用您在 [移轉] 命令中指定的張貼移轉 DNS 名稱清空的標準命名空間實體的指令碼。 將這些訊息傳送給進階命名空間，讓接收者可以處理它們。

訊息已清空後，刪除標準命名空間。

>[!IMPORTANT]
> 將訊息從標準的命名空間已清空後，刪除標準命名空間。 這很重要，因為一開始現在稱為標準命名空間的連接字串是指進階命名空間。 您不會在不再需要的標準命名空間。 刪除您所移轉的標準命名空間，可協助減少更新版本的混淆。

### <a name="how-much-downtime-do-i-expect"></a>我預期多少停機時間？
移轉程序的目的是要減少應用程式的預期停機時間。 停機時間減少使用的連接字串以指向新的進階命名空間中使用的傳送者與接收者應用程式。

所經歷的應用程式的停機時間僅限於更新為指向進階命名空間的 DNS 項目所花費的時間。 停機時間是大約 5 分鐘。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>我有在執行移轉時進行任何組態變更嗎？
否，沒有執行移轉所需的程式碼或組態變更。 寄件者與接收者應用程式用來存取標準命名空間的連接字串會自動對應至做為進階命名空間的別名。

### <a name="what-happens-when-i-abort-the-migration"></a>當我中止移轉時，會發生什麼事？
移轉可能會中止使用`Abort`命令，或使用 Azure 入口網站。 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure 入口網站

![中止流程-中止同步處理][]
![中止流程-中止完成][]

中止移轉程序，它會中止複製從標準的實體 （主題、 訂用帳戶和篩選），為進階命名空間的程序，並會中斷配對。

連接字串不會更新為指向進階命名空間。 您現有的應用程式會繼續以您在開始移轉之前一樣運作。

不過，它不會刪除在進階命名空間上的實體，或刪除進階命名空間。 如果您不打算繼續進行移轉，以手動方式刪除實體。

>[!IMPORTANT]
> 如果您決定中止移轉，請刪除進階命名空間中，您必須佈建的移轉，讓不計費的資源。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想要清空訊息。 該怎麼辦？

可能的寄件者應用程式所傳送及移轉進行時，只有認可移轉之前，只是致力標準命名空間上的儲存體的訊息。

在移轉期間，實際的訊息資料/內容不是從標準複製到進階命名空間。 訊息必須以手動方式清空，然後傳送至的進階命名空間。

不過，如果您可以將移轉計劃的維護/清理期間，您不想要手動清空，並傳送訊息，請遵循下列步驟：

1. 停止傳送者應用程式。 接收者應用程式會處理訊息，目前在標準的命名空間中，將會清空佇列。
1. 是空的佇列和訂用帳戶中的標準命名空間之後，請遵循先前所述執行進階命名空間從標準移轉程序。
1. 完成移轉之後，您可以重新啟動的傳送者應用程式。
1. 傳送者和接收者現在會自動連線與進階命名空間。

    >[!NOTE]
    > 您不必停止接收者應用程式移轉。
    >
    > 完成移轉之後，接收者應用程式將會中斷與標準的命名空間的連線，並自動連線到進階命名空間。

## <a name="next-steps"></a>後續步驟

* 深入了解[差異標準和進階傳訊](./service-bus-premium-messaging.md)。
* 深入了解[服務匯流排進階版的高可用性和地理災害復原層面](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)。

[移轉登陸頁面]: ./media/service-bus-standard-premium-migration/1.png
[設定命名空間]: ./media/service-bus-standard-premium-migration/2.png
[設定命名空間-建立進階命名空間]: ./media/service-bus-standard-premium-migration/3.png
[設定命名空間-挑選 post 移轉名稱]: ./media/service-bus-standard-premium-migration/4.png
[設定命名空間-同步處理實體-開始]: ./media/service-bus-standard-premium-migration/5.png
[設定命名空間-同步處理實體-進度]: ./media/service-bus-standard-premium-migration/8.png
[切換命名空間-切換功能表]: ./media/service-bus-standard-premium-migration/9.png
[參數命名空間-成功]: ./media/service-bus-standard-premium-migration/12.png

[中止流程-中止同步處理]: ./media/service-bus-standard-premium-migration/abort1.png
[中止流程-中止完成]: ./media/service-bus-standard-premium-migration/abort3.png
