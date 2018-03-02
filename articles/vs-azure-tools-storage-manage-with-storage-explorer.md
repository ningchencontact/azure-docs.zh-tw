---
title: "開始使用儲存體總管 (預覽) | Microsoft Docs"
description: "使用儲存體總管管理 Azure 儲存體資源 (預覽)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 27b3775d81ec6dc093dae4ee46167c5d5a9c9e19
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>開始使用儲存體總管 (預覽)
## <a name="overview"></a>概觀
Azure 儲存體總管 (預覽) 是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 在本文中，您將了解連線及管理 Azure 儲存體帳戶的數種方式。

![Microsoft Azure 儲存體 Explorer (預覽)][0]

## <a name="prerequisites"></a>先決條件
* [下載並安裝儲存體總管 (預覽)](http://www.storageexplorer.com)

> [!NOTE]
> 針對 Ubuntu 16.04 以外的 Linux 散發版本，您可能需要手動安裝某些相依性。 一般而言，必要的套件如下：
> * libgconf-2-4
> * libsecret
> * 最新的 GCC
>
> 依據您的散發版本，您可能需要安裝其他套件。 儲存體總管[版本資訊](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)包含某些散發版本的特定步驟。
>
>

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務
儲存體總管 (預覽) 提供數種方式來連線至儲存體帳戶。 例如，您可以：
* 連線至與您的 Azure 訂用帳戶相關聯的儲存體帳戶。
* 連線至從其他 Azure 訂用帳戶共用的儲存體帳戶和服務。
* 使用 Azure 儲存體模擬器連線到本機儲存體並加以管理。 

此外，您可以使用全球和國家 Azure 中的儲存體帳戶：

* [連線至 Azure 訂用帳戶](#connect-to-an-azure-subscription)：管理屬於您的 Azure 訂用帳戶的儲存體資源。
* [使用本機開發儲存體](#work-with-local-development-storage)：使用 Azure 儲存體模擬器管理本機儲存體。
* [連接至外部儲存體](#attach-or-detach-an-external-storage-account)：使用儲存體帳戶的名稱、金鑰和端點，管理屬於另一個 Azure 訂用帳戶或在國家 Azure 雲端下的儲存體資源。
* [使用 SAS 連結儲存體帳戶](#attach-storage-account-using-sas)：使用共用存取簽章 (SAS)，管理屬於另一個 Azure 訂用帳戶的儲存體資源。
* [使用 SAS 連結服務](#attach-service-using-sas)：使用 SAS，管理屬於另一個 Azure 訂用帳戶的特定儲存體服務 (Blob 容器、佇列或資料表)。
* [使用連接字串連線到 Azure Cosmos DB 帳戶](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)：使用連接字串管理 Cosmos DB 帳戶。

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶
> [!NOTE]
> 如果您沒有 Azure 帳戶，可以[申請免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。
>
>

1. 在儲存體總管 (預覽) 中，選取 [管理帳戶] 以移至 [帳戶管理面板]。

    ![管理帳戶][1]

2. 左窗格現在會顯示您已登入的所有 Azure 帳戶。 若要連線到另一個帳戶，請選取 [新增帳戶]

3. 如果您要登入國家雲端或 Azure Stack，請按一下 [Azure 環境] 下拉式清單以選取您要使用的 Azure 雲端。 選擇您的環境後，按一下 [登入...] 按鈕。 如果您要登入 Azure Stack，請參閱[將儲存體總管連線到 Azure Stack 訂用帳戶](azure-stack/user/azure-stack-storage-connect-se.md)以取得詳細資訊。

    ![登入選項][2]

3. 使用 Azure 帳戶成功登入後，左窗格中會新增該帳戶以及與該帳戶相關聯的 Azure 訂用帳戶。 選取您要使用的 Azure 訂用帳戶，然後選取 [套用] (選取 [所有訂用帳戶:] 切換方塊，可選取全部或不選取任何列出的 Azure 訂用帳戶)。

    ![選取 Azure 訂用帳戶][3]

    左窗格會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。

    ![已選取的 Azure 訂用帳戶][4]

## <a name="work-with-local-development-storage"></a>使用本機開發儲存體
使用儲存體總管 (預覽)，您可以使用 Azure 儲存體模擬器來處理本機儲存體。 此方法可讓您模擬使用 Azure 儲存體，而不需在 Azure 上部署儲存體帳戶，因為 Azure 儲存體模擬器正在模擬儲存體帳戶。

> [!NOTE]
> Azure 儲存體模擬器目前只適用於 Windows。
>
>

> [!NOTE]
> Azure 儲存體模擬器不支援檔案共用。
>
>

1. 在儲存體總管 (預覽) 的左窗格中，依序展開 [(本機連結)] > [儲存體帳戶] > [(開發)] > [Blob 容器] 節點。

    ![本機開發節點][5]

2. 如果您尚未安裝 Azure 儲存體模擬器，系統會透過資訊列提示您這麼做。 若已顯示資訊列，請選取 [下載最新版本] ，然後安裝模擬器。

    ![下載 Azure 儲存體模擬器提示字元][6]

3. 安裝模擬器之後，您即可建立並使用本機 blob、佇列和資料表。 若要了解如何使用每個儲存體帳戶類型，請參閱下列指南：

    * [管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
    * 管理 Azure 檔案共用儲存體資源：敬請期待 
    * 管理 Azure 佇列儲存體資源：敬請期待 
    * 管理 Azure 表格儲存體資源：敬請期待 

## <a name="attach-or-detach-an-external-storage-account"></a>附加或卸離外部儲存體帳戶
使用儲存體總管 (預覽)，您可以連結至外部儲存體帳戶，因此可以輕鬆地共用儲存體帳戶。 本節說明如何附加至 (及卸離) 外部儲存體帳戶。

### <a name="get-the-storage-account-credentials"></a>取得儲存體帳戶認證
若要共用外部儲存體帳戶，該帳戶的擁有者必須先取得帳戶的認證 (帳戶名稱和金鑰)，然後與想要連結至所述帳戶的人員分享該資訊。 執行下列步驟，即可透過 Azure 入口網站取得儲存體帳戶認證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [瀏覽] 。

3. 選取 [儲存體帳戶] 。

4. 在 [儲存體帳戶] 清單中，選取所需的儲存體帳戶。

5. 在 [設定] 底下，選取 [存取金鑰]。

    ![存取金鑰選項][7]

6. 複製 [儲存體帳戶名稱] 和 [金鑰1]。

    ![存取金鑰][8]

### <a name="attach-to-an-external-storage-account"></a>附加至外部儲存體帳戶
若要附加至外部儲存體帳戶，您需要帳戶的名稱和金鑰。 「取得儲存體帳戶認證」一節說明如何從 Azure 入口網站取得這些值。 不過，在入口網站中，帳戶金鑰稱為 [金鑰1]。 因此，當儲存體總管 (預覽) 要求帳戶金鑰時，您會輸入 [金鑰 1] 值。

1. 在儲存體總管 (預覽) 中，開啟 [連線對話方塊]。

    ![連接至 Azure 儲存體選項][9]

2. 在 [連線對話方塊] 中，選擇 [使用儲存體帳戶名稱和金鑰]

    ![使用名稱和金鑰選項進行新增][10]

3. 在 [帳戶名稱] 文字方塊中貼上您的帳戶名稱，以及在 [帳戶金鑰] 文字方塊中貼上您的帳戶金鑰 (來自 Azure 入口網站的 [金鑰1] 值)，然後選取 [下一步]。

    ![名稱和金鑰頁面][11]

    > [!NOTE]
    > 若要使用來自國家雲端的名稱和金鑰，請使用 [儲存體端點網域:] 下拉式清單來選取適當的端點網域： 
    >
    >

4. 在 [連線摘要] 對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁] ，並重新輸入所需的設定。 

5. 選取 [ **連接**]。

6. 成功連結儲存體帳戶之後，顯示的儲存體帳戶會在其名稱後面附加 **(外部)**。

    ![連接至外部儲存體帳戶的結果][12]

### <a name="detach-from-an-external-storage-account"></a>從外部儲存體帳戶卸離
1. 以滑鼠右鍵按一下您要卸離的外部儲存體帳戶，然後選取 [卸離]。

    ![中斷與儲存體選項的連結][13]

2. 在確認訊息中，選取 [是] 以確認從外部儲存體帳戶卸離。

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>使用共用存取簽章 (SAS) 連結儲存體帳戶
共用存取簽章 (或 [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)) 可讓 Azure 訂用帳戶的系統管理員授與儲存體帳戶的暫時存取權，而不需提供 Azure 訂用帳戶認證。

為了說明此案例，我們假設 UserA 是 Azure 訂用帳戶的系統管理員，而且 UserA 想要允許 UserB 在有限的時間內使用特定權限存取儲存體帳戶：

1. UserA 可在特定期間內使用所需的權限產生 SAS 連接字串。

2. UserA 可與想要存取儲存體帳戶的人員 (在本例中為 UserB) 共用 SAS。  

3. UserB 可透過儲存體總管 (預覽)，使用所提供的 SAS 來連結至屬於 UserA 的帳戶。

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>針對您要共用的帳戶產生 SAS 連接字串
1. 在儲存體總管 (預覽) 中，以滑鼠右鍵按一下您要共用的儲存體帳戶，然後選取 [取得共用存取簽章...]。

    ![取得 SAS 內容功能表選項][14]

2. 在 [產生共用存取簽章] 對話方塊中，指定您要用於此帳戶的時間範圍和權限，然後按一下 [建立] 按鈕。

    ![取得 SAS 對話方塊][15]  

3. 選取 [連接字串] 文字方塊旁的 [複製]，將它複製到剪貼簿，然後按一下 [關閉]。

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>使用 SAS 連接字串連結儲存體帳戶
1. 在儲存體總管 (預覽) 中，開啟 [連線對話方塊]。

    ![連接至 Azure 儲存體選項][9]

2. 在 [連線對話方塊] 對話方塊中，選擇 [使用連接字串或共用存取簽章 URI]，然後按 [下一步]。

    ![連線至 Azure 儲存體對話方塊][16]

3. 選擇 [使用連接字串] 並且在 [連接字串:] 欄位中貼上您的連接字串。 按 [下一步] 按鈕。

    ![連線至 Azure 儲存體對話方塊][17]

4. 在 [連線摘要] 對話方塊中，確認資訊。 若要進行變更，請選取 [上一頁]，然後輸入您想要的設定。 

5. 選取 [ **連接**]。

6. 成功連結儲存體帳戶之後，顯示的儲存體帳戶會在其名稱後面附加 **(SAS)**。

    ![使用 SAS 連結至帳戶的結果][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>使用共用存取簽章 (SAS) 連結服務
「使用 SAS 連結儲存體帳戶」一節說明 Azure 訂用帳戶系統管理員如何藉由產生及共用儲存體帳戶的 SAS，來授與儲存體帳戶的暫存存取權。 同樣地，可以針對儲存體帳戶內的特定服務 (Blob 容器、佇列、資料表或檔案共用) 產生 SAS。  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>針對您要共用的服務產生 SAS
在此情況下，服務可以是 Blob 容器、佇列、資料表或檔案共用。 若要產生所列服務的 SAS，請參閱︰

* [取得 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* 取得檔案共用的 SAS：敬請期待
* 取得佇列的 SAS：敬請期待
* 取得資料表的 SAS：敬請期待

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>使用 SAS URI 連結至共用帳戶服務
1. 在儲存體總管 (預覽) 中，開啟 [連線對話方塊]。

    ![連接至 Azure 儲存體選項][9]

2. 在 [連線對話方塊] 對話方塊中，選擇 [使用連接字串或共用存取簽章 URI]，然後按 [下一步]。

    ![連線至 Azure 儲存體對話方塊][16]

3. 選擇 [使用 SAS URI] 並且在 [URI:] 欄位中貼上您的 URI。 按 [下一步] 按鈕。

    ![連線至 Azure 儲存體對話方塊][19]

3. 在 [連線摘要] 對話方塊中，確認資訊。 若要進行變更，請選取 [上一頁]，然後輸入您想要的設定。 

4. 選取 [ **連接**]。

5. 成功連結服務之後，此服務會顯示在 [(SAS 連結的服務)] 節點之下。

    ![使用 SAS 連結至共用服務的結果][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB 帳戶
除了透過 Azure 訂用帳戶管理 Azure Cosmos DB 帳戶，另一種連線到 Azure Cosmos DB 的方法是使用連接字串。 使用下列步驟來使用連接字串進行連線。

1. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)，以滑鼠右鍵按一下 [Azure Cosmos DB 帳戶]，選擇 [連線到 Azure Cosmos DB...]

    ![透過連接字串連線到 Azure Cosmos DB][21]

2. 選擇 Azure Cosmos DB API，貼上您的 [連接字串]，然後按一下 [確定] 以連接 Azure Cosmos DB 帳戶。 如需擷取連接字串的資訊，請參閱[取得連接字串](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![connection-string][22]

 ## <a name="connect-to-azure-data-lake-store-by-uri"></a>以 URI 連線至 Azure Data Lake Store
如果您想要取得資源的存取權，而這些資源不存在於您的訂用帳戶中， 但他人授權給您取得資源的 URI。 在此情況下，您可以在登入後使用 URI 連線至 Data Lake Store。 請參閱下列步驟。
1. 開啟儲存體 Explorer (預覽)。
2. 在左窗格中，展開 [本機與已連結的資源]。
3. 以滑鼠右鍵按一下 **Data Lake Store**，然後從內容功能表中選取 [連線至 Data Lake Store...]。

    ![連線至 Data Lake Store 內容功能表](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 輸入 URI，工具即會瀏覽至您剛才輸入的 URL 位置。

    ![連線至 Data Lake Store 內容對話方塊](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![連線至 Data Lake Store 結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶
如果您需要尋找儲存體資源，但不知道其所在位置，您可以使用左窗格頂端的搜尋方塊來搜尋此資源。

在搜尋方塊中輸入資料時，左窗格會顯示與您在當時為止已輸入的搜尋值相符的所有資源。 例如，下列螢幕擷取畫面會顯示針對**端點**所進行的搜尋：

![儲存體帳戶搜尋][23]

> [!NOTE]
> 使用 [帳戶管理面板] 來取消選取任何不包含您要搜尋之項目的訂用帳戶，以改善搜尋的執行時間。 您也可以在節點上按一下滑鼠右鍵，並選擇 [從這裡搜尋] 以從特定節點開始搜尋。
>
>

## <a name="next-steps"></a>後續步驟
* [使用儲存體總管 (預覽) 來管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
* [在 Azure 儲存體總管 (預覽) 中管理 Azure Cosmos DB](./cosmos-db/storage-explorer.md)
* [使用儲存體總管來管理 Azure Data Lake Store 資源 (預覽)](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
