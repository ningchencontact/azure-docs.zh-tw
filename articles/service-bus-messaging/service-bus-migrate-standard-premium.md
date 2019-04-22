---
title: 將現有 Azure 服務匯流排標準命名空間移轉到 Premium 層 |Microsoft Docs
description: 若要允許的現有 Azure 服務匯流排標準命名空間為 premium Edition 移轉指南
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
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896320"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>將現有 Azure 服務匯流排標準命名空間移轉至進階層

先前，Azure 服務匯流排提供只能在標準層上的命名空間。 這兩個已針對低的輸送量和最佳化開發人員環境的多租用戶設定。

最近，Azure 服務匯流排已擴充為提供進階層提供每個命名空間的可預測的延遲並增加的輸送量，其中最適合用於高輸送量和生產環境以固定價格的專用的資源需要額外的企業功能。

下列工具可讓現有的標準層命名空間移轉到 「 進階 」 層。

>[!WARNING]
> 移轉時，就是服務匯流排標準命名空間上，要***升級***為進階層。
>
> 移轉工具***則否***支援降級。
>[!NOTE]
> 此移轉為了在發生***就地***。
>
> 這表示現有的傳送者與接收者應用程式不需要任何程式碼或組態變更。
>
> 現有的連接字串會自動指向新的進階命名空間。
>
> 此外，標準的命名空間中的所有實體都都可**複製**進階命名空間，在移轉過程中。
>
>
> 我們支援***1,000 個實體，每個傳訊單位***在 Premium，因此若要找出多少個傳訊單位中，您需要請使用啟動您已在您目前的標準命名空間的實體數目。

## <a name="migration-steps"></a>移轉步驟

>[!IMPORTANT]
> 有幾個移轉程序相關聯的警告訊息。 我們會要求您完全熟悉的步驟來減少錯誤的可能性。

下列指南會詳細說明具體的逐步解說移轉程序。

所需的邏輯步驟包括-

1. 建立新的進階命名空間。
2. 配對彼此的標準和進階命名空間。
3. 將實體從標準同步處理 （透過複製） 為進階命名空間
4. 認可移轉
5. 清空標準使用命名空間的移轉後名稱的命名空間中的實體
6. 刪除標準命名空間

>[!NOTE]
> 一旦移轉已認可，它是非常重要的事存取舊的標準命名空間和清空佇列和訂用帳戶。
>
> 一旦訊息已被清空他們可能會傳送至新的進階命名空間由接收者應用程式來處理。
>
> 一旦清空佇列和訂用帳戶，建議您刪除舊的標準命名空間。 您將不會需要它 ！

### <a name="migrate-using-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 進行移轉

若要為使用 Azure CLI 或 PowerShell 工具的高階移轉您的服務匯流排標準命名空間，請參閱下列指南。

1. 建立新的服務匯流排進階命名空間。 您可以參考[Azure Resource Manager 範本](service-bus-resource-manager-namespace.md)或是[使用 Azure 入口網站](service-bus-create-namespace-portal.md)。 請務必選取 「 進階 」 **serviceBusSku**參數。

2. 設定以下環境變數，以簡化移轉命令。
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > 移轉後名稱 (post_migration_dns_name) 將用於存取舊的標準命名空間移轉後。 您必須使用此清空佇列和訂用帳戶，然後再刪除命名空間。

3. **配對**Standard 和 Premium 的命名空間並**開始同步處理**使用下列命令-

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. 檢查狀態移轉使用下列命令-
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    移轉會被視為完成時
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    此命令也會顯示移轉設定。 請再次檢查，以確保值都會設為前一個宣告。

    此外，也請檢查入口網站，以確保所有佇列和主題都建立之後，且兩者相符項目存在於上標準的命名空間中的進階命名空間。

5. 認可移轉執行完整的命令
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>使用 Azure 入口網站進行移轉

透過 Azure 入口網站移轉具有相同的邏輯流程，做為移轉使用的命令。 請參閱下方的逐步程序將使用入口網站的指南。

1. 挑選 **'移轉至進階'** 從導覽功能表的功能表選項，在左窗格中。 按一下  **「 立即開始使用 '** 按鈕以繼續前往下一個頁面。
    ![移轉登陸頁面][]

2. 完整**安裝程式**。
   ![設定命名空間][]
   1. 建立並指派進階命名空間移轉到現有的標準命名空間。
        ![設定命名空間-建立進階命名空間][]
   2. 挑選 **'Post 移轉 name'** 存取透過標準的命名空間，在移轉完成之後。
        ![設定命名空間-挑選 post 移轉名稱][]
   3. 按一下  **'Next'** 以繼續。
3. **同步處理**實體之間的標準和進階命名空間。
    ![設定命名空間-同步處理實體-開始][]

   1. 按一下  **'開始同步處理'** 開始同步處理的實體。
   2. 按一下  **'Yes'** 上快顯視窗中確認開始同步處理。
   3. 等到**同步**完成。 狀態是在網站上 [狀態列]。
        ![設定命名空間-同步處理實體-進度][]
        >[!IMPORTANT]
        > 如果您需要**中止**因為任何原因，請檢閱這份文件常見問題集一節中的中止流程。
   4. 在同步處理完成後，按一下 **'Next'** 在頁面底部的按鈕。

4. 檢閱 [摘要] 頁面上的變更。
    ![切換命名空間-切換功能表][]

5. 按一下  **「 完成移轉 」** 切換命名空間，並完成移轉。
    ![參數命名空間-成功][]

## <a name="faqs"></a>常見問題集

### <a name="what-happens-when-the-migration-is-committed"></a>認可移轉時，會發生什麼事？

認可移轉後，指向標準命名空間的連接字串會指向進階命名空間中。

寄件者與接收者應用程式會中斷與標準的命名空間的連線，並自動重新連線到進階命名空間。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>該怎麼辦標準版至進階移轉完成之後？

標準版至進階移轉可確保該實體中繼資料 （主題、 訂用帳戶、 篩選條件等等） 都會覆寫從標準進階命名空間。 標準的命名空間所認可的訊息資料不是複製從標準到進階命名空間。

由於這個原因，標準的命名空間可能會有某些訊息已傳送及認可時移轉正在進行中。 這些訊息必須以手動方式清空從標準的命名空間，以手動方式傳送透過進階命名空間。

若要這樣做，您***必須***使用主控台應用程式或指令碼，使用標準命名空間實體會耗盡**Post 移轉 DNS 名稱**上的移轉命令中指定，並再傳送訊息進階命名空間，讓接收者可以處理它們。

一旦訊息已清空，請繼續進行刪除標準命名空間。

>[!IMPORTANT]
> 請注意，標準的命名空間中的訊息已清空，一旦您**必須**刪除標準命名空間。
>
> 這很重要，因為一開始現在稱為標準命名空間的連接字串實際上參考進階命名空間。 您將不再需要這個標準的命名空間不再。
>
> 刪除您移轉協助的標準命名空間，可減少在日後造成混淆。 

### <a name="how-much-downtime-do-i-expect"></a>我預期多少停機時間？
上述的移轉程序的目的是要減少應用程式的預期停機時間。 這是藉由使用傳送者與接收者應用程式會使用以指向新的進階命名空間的連接字串。

應用程式所遇到的停機時間僅限於更新為指向進階命名空間的 DNS 項目所花費的時間量。

這可以是假設***大約 5 分鐘***。

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>我有在執行移轉時進行任何組態變更嗎？
否，沒有執行此移轉所需的程式碼/組態變更。 寄件者與接收者應用程式用來存取標準命名空間的連接字串會自動對應到充當**別名**進階命名空間。

### <a name="what-happens-when-i-abort-the-migration"></a>當我中止移轉時，會發生什麼事？
使用 'Abort' 命令或透過 Azure 入口網站，則可以中止移轉。 

#### <a name="azure-cli-or-powershell"></a>Azure CLI 或 PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Azure 入口網站

![中止流程-中止同步處理][]
![中止流程-中止完成][]

當中止移轉程序時，它實際上從標準中止複製的實體 （主題、 訂用帳戶和篩選） 的程序，為進階命名空間，並會中斷配對。

連接字串**不是**更新為指向進階命名空間。 您現有的應用程式會繼續以您在開始移轉之前一樣運作。

不過，它**則否**刪除進階命名空間的實體，或刪除本身的進階命名空間。 這有如果您已決定不繼續進行移轉之後以手動方式完成。

>[!IMPORTANT]
> 如果您決定中止移轉，請刪除您在移轉時，必須佈建進階命名空間，以便不計費的資源。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想要清空訊息。 該怎麼辦？

可能的寄件者應用程式所傳送及移轉進行時，致力於標準的命名空間的儲存體的訊息，是認可移轉之前的權限。

假設在移轉期間，實際訊息資料/內容時不會複製從標準到 Premium，這些必須手動清空，然後傳送至的進階命名空間。

不過，如果您可以將移轉計劃的維護維護時段期間並不想要手動清空，並傳送訊息時，請遵循下列步驟-

1. 停止的傳送者應用程式，並允許處理目前均為標準的命名空間與清空佇列之訊息的接收者。
2. 當佇列和訂用帳戶的標準命名空間中是空的時請遵循上面所述，從 Standard 執行進階命名空間移轉的程序。
3. 移轉完成之後，您可以重新啟動的傳送者應用程式。
4. 傳送者和接收者現在會自動連線與進階命名空間。

    >[!NOTE]
    > 接收端不需要停止移轉。
    >
    > 移轉完成後，接收者會中斷與標準的命名空間的連線，並自動連線到進階命名空間。

## <a name="next-steps"></a>後續步驟

* 深入了解[標準和進階傳訊之間的差異](./service-bus-premium-messaging.md)
* 了解的高可用性及損毀修復能力異地復原方面的服務匯流排進階[這裡](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

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
