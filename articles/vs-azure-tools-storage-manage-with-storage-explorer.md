---
title: 開始使用儲存體總管 | Microsoft Docs
description: 使用儲存體總管來管理 Azure 儲存體資源
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508748"
---
# <a name="get-started-with-storage-explorer"></a>開始使用儲存體總管

## <a name="overview"></a>概觀

Azure 儲存體總管是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 在本文中，您將了解連線及管理 Azure 儲存體帳戶的數種方式。

![Microsoft Azure 儲存體總管][0]

## <a name="prerequisites"></a>必要條件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

下列的 Windows 版本支援 Azure 儲存體總管：

* Windows 10 (建議採用)
* Windows 8
* Windows 7

所有 Windows 版本都需要 .NET Framework 4.6.2 或更新版本。

[下載並安裝儲存體總管](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

下列的 macOS 版本支援 Azure 儲存體總管：

* macOS 10.12 "Sierra" 及更新版本

[下載並安裝儲存體總管](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

下列 Linux 發行版本支援 Azure 儲存體總管：

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Azure 儲存體總管或許可以在其他發行版本上執行，但官方僅支援上面列出的發行版本。

在 Linux 上安裝儲存體總管的詳細說明，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

Azure 儲存體總管[版本資訊](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)包含某些發行版本的特定步驟。

[下載並安裝儲存體總管](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務

儲存體總管提供數種方式來連線至儲存體帳戶。 一般情況下您可以：

* [登入 Azure 以存取您的訂用帳戶和其資源](#sign-in-to-azure)
* [附加特定的儲存體或 CosmosDB 資源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登入 Azure

> [!NOTE]
> 若要登入後，完全存取的資源，儲存體總管會需要管理 (ARM) 和資料層級權限。 這表示您需要 Azure AD 權限，讓您存取您的儲存體帳戶，在帳戶中，容器，並在容器中的資料。 如果您只有在資料層級權限，請考慮使用[與 Azure AD 的附加](#add-a-resource-via-azure-ad)。 如需有關儲存體總管需要的確切權限的詳細資訊，請參閱 <<c0> [ 疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)。
>
>

1. 在儲存體總管中，選取 [管理帳戶]  以移至 [帳戶管理面板]  。

    ![管理帳戶][1]

2. 左窗格現在會顯示您已登入的所有 Azure 帳戶。 若要連線到另一個帳戶，請選取 [新增帳戶] 

3. 如果您要登入國家雲端或 Azure Stack，請按一下 [Azure 環境]  下拉式清單以選取您要使用的 Azure 雲端。 選擇您的環境後，按一下 [登入...]  按鈕。 如果您要登入 Azure Stack，請參閱[將儲存體總管連線到 Azure Stack 訂用帳戶](/azure-stack/user/azure-stack-storage-connect-se)以取得詳細資訊。

    ![登入選項][2]

4. 使用 Azure 帳戶成功登入後，左窗格中會新增該帳戶以及與該帳戶相關聯的 Azure 訂用帳戶。 選取您要使用的 Azure 訂用帳戶，然後選取 [套用]  \(選取 [所有訂用帳戶:]  切換方塊，可選取全部或不選取任何列出的 Azure 訂用帳戶)。

    ![選取 Azure 訂用帳戶][3]

    左窗格會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。

    ![已選取的 Azure 訂用帳戶][4]

### <a name="attach-a-specific-resource"></a>附加特定的資源
    
有各種不同的選項將資源連結至儲存體總管。 您可以：

* [新增透過 Azure AD 資源](#add-a-resource-via-azure-ad):如果您只有在資料層級權限，然後您就可以使用此選項將 Blob 容器或 ADLS Gen2 Blob 容器。
* [使用連接字串](#use-a-connection-string):如果您有儲存體帳戶的連接字串。 儲存體總管支援這兩個金鑰並[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)連接字串。
* [使用 SAS URI](#use-a-sas-uri):如果您有[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) Blob 容器、 檔案共用、 佇列或資料表的 URI。 若要取得 SAS URI，您可以使用[儲存體總管](#generate-a-sas-in-storage-explorer)或[Azure 入口網站](https://portal.azure.com)。
* [使用名稱和金鑰](#use-a-name-and-key):如果您知道任一帳戶金鑰儲存體帳戶，您可以使用此選項，若要快速連線。 儲存體帳戶金鑰位於儲存體帳戶**存取金鑰**刀鋒視窗[Azure 入口網站](https://portal.azure.com)。
* [附加到本機模擬器](#attach-to-a-local-emulator):如果您使用其中一個可用的 Azure 儲存體模擬器，請使用此選項以輕鬆地連接到您的模擬器。
* [使用連接字串連線到 Azure Cosmos DB 帳戶](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)：如果您擁有的 CosmosDB 執行個體的連接字串。
* [連接到 Azure Data Lake Store 的 URI](#connect-to-azure-data-lake-store-by-uri):如果您需要 Azure Data Lake Store 的 URI。

#### <a name="add-a-resource-via-azure-ad"></a>新增透過 Azure AD 資源

1. 開啟**連線對話方塊**上按一下 **[連線] 按鈕**左側，垂直工具列。

    ![連接至 Azure 儲存體選項][9]

2. 如果您尚未這樣做，使用**新增 Azure 帳戶**登入有資源的存取權的 Azure 帳戶的選項。 登入後在 退貨**連線對話方塊**。

3. 選取 [**新增資源，以透過 Azure Active Directory (Azure AD)** 選項，然後按一下**下一步]** 。

4. 選取有您想要附加的儲存體資源和訂用帳戶中的資源存取權的 Azure 帳戶，然後按一下**下一步**。

5. 選擇您想要附加的資源類型，然後輸入 連線所需的資訊。 在此頁面上輸入，將視您要新增的資源的類型而定。 請務必選擇正確的資源類型。 一旦您填寫必要的資訊，請按一下**下一步**。

6. 檢閱摘要的連線，並確定所有資訊都正確。 如果所有的資訊看起來正確然後按一下**Connect**，否則會傳回與前幾頁**回**按鈕，以更正任何錯誤的資訊。

連接已成功新增之後，資源樹狀目錄會自動巡覽至代表連接的節點上。 如果基於某些原因它，看起來不下**本機與已連結**→**儲存體帳戶**→ **（附加的容器）** → **Blob 容器**. 如果儲存體總管無法新增您的連線，或如果您無法存取您的資料已成功新增連線 之後，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)取得協助。

#### <a name="use-a-connection-string"></a>使用連接字串

1. 開啟**連線對話方塊**上按一下 **[連線] 按鈕**左側，垂直工具列。

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用的連接字串**選項，然後按一下**下一步]** 。

3. 選擇您的連線的顯示名稱，並輸入您的連接字串。 然後按 [下一步]  。

4. 檢閱摘要的連線，並確定所有資訊都正確。 如果所有的資訊看起來正確然後按一下**Connect**，否則會傳回與前幾頁**回**按鈕，以更正任何錯誤的資訊。

連接已成功新增之後，資源樹狀目錄會自動巡覽至代表連接的節點上。 如果基於某些原因它，看起來不下**本機與已連結**→**儲存體帳戶**。 如果儲存體總管無法新增您的連線，或如果您無法存取您的資料已成功新增連線 之後，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)取得協助。

#### <a name="use-a-sas-uri"></a>使用 SAS URI

1. 開啟**連線對話方塊**上按一下 **[連線] 按鈕**左側，垂直工具列。

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用共用的存取簽章 (SAS) URI**選項，然後按一下**下一步]** 。

3. 選擇您的連線的顯示名稱，並輸入您的 SAS URI。 您要連結的資源類型的服務端點應自動填滿。 如果您正在使用自訂端點，則可能不可能。 单击“下一步”  。

4. 檢閱摘要的連線，並確定所有資訊都正確。 如果所有的資訊看起來正確然後按一下**Connect**，否則會傳回與前幾頁**回**按鈕，以更正任何錯誤的資訊。

連接已成功新增之後，資源樹狀目錄會自動巡覽至代表連接的節點上。 如果基於某些原因它，看起來不下**本機與已連結**→**儲存體帳戶**→ **（附加的容器）** →**類型的 [服務] 節點您所附加的容器**。 如果儲存體總管無法新增您的連線，或如果您無法存取您的資料已成功新增連線 之後，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)取得協助。

#### <a name="use-a-name-and-key"></a>使用名稱和金鑰

1. 開啟**連線對話方塊**上按一下 **[連線] 按鈕**左側，垂直工具列。

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用儲存體帳戶名稱和金鑰**選項，然後按一下**下一步]** 。

3. 選擇您的連線的顯示名稱。

4. 輸入您的儲存體帳戶名稱和其中一個存取金鑰。

5. 選擇**儲存體網域**，然後按一下**下一步**。

4. 檢閱摘要的連線，並確定所有資訊都正確。 如果所有的資訊看起來正確然後按一下**Connect**，否則會傳回與前幾頁**回**按鈕，以更正任何錯誤的資訊。

連接已成功新增之後，資源樹狀目錄會自動巡覽至代表連接的節點上。 如果基於某些原因它，看起來不下**本機與已連結**→**儲存體帳戶**。 如果儲存體總管無法新增您的連線，或如果您無法存取您的資料已成功新增連線 之後，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)取得協助。

#### <a name="attach-to-a-local-emulator"></a>附加到本機模擬器

儲存體總管支援所有平台上的模擬器。 目前官方可用模擬器兩者：
* [Azure 儲存體模擬器](storage/common/storage-use-emulator.md)(僅 Windows)
* [Azurite](https://github.com/azure/azurite) （Windows、 macOS 或 Linux）

如果您的模擬器正在執行的預設連接埠上您可以使用**模擬器-預設連接埠**節點，一律可以下找到**本機與已連結**→**儲存體帳戶**若要快速存取您的模擬器。 如果您想要使用不同的名稱，為您的連線，或如果您的模擬器不會執行預設連接埠，請遵循下列步驟。

1. 啟動您的模擬器。 當您執行動作，請記下的哪些連接埠模擬器正在接聽的每個服務類型。 您必須知道這項資訊更新版本。

   > [!IMPORTANT]
   > 儲存體總管不會自動啟動您的模擬器。 您必須自行加以啟動。

2. 開啟**連線對話方塊**上按一下 **[連線] 按鈕**左側，垂直工具列。

    ![連接至 Azure 儲存體選項][9]

3. 選取 [**附加到本機模擬器**選項，然後按一下**下一步]** 。

4. 選擇您的連線的顯示名稱，並輸入您的模擬器正在接聽的每個服務類型的連接埠。 根據預設，文字方塊中會包含大部分的模擬器的預設連接埠值。 **檔案的連接埠**也保留空白預設都不正式的模擬器目前支援 「 檔案 」 服務。 如果您使用的模擬器不支援它不過，您可以輸入的連接埠正在使用中。 单击“下一步”  。

5. 檢閱摘要的連線，並確定所有資訊都正確。 如果所有的資訊看起來正確然後按一下**Connect**，否則會傳回與前幾頁**回**按鈕，以更正任何錯誤的資訊。

連接已成功新增之後，資源樹狀目錄會自動巡覽至代表連接的節點上。 如果基於某些原因它，看起來不下**本機與已連結**→**儲存體帳戶**。 如果儲存體總管無法新增您的連線，或如果您無法存取您的資料已成功新增連線 之後，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)取得協助。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB 帳戶

除了透過 Azure 訂用帳戶管理 Azure Cosmos DB 帳戶，另一種連線到 Azure Cosmos DB 的方法是使用連接字串。 使用下列步驟來使用連接字串進行連線。

1. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)  ，以滑鼠右鍵按一下 [Azure Cosmos DB 帳戶]  ，選擇 [連線到 Azure Cosmos DB...] 

    ![透過連接字串連線到 Azure Cosmos DB][21]

2. 選擇 Azure Cosmos DB API，貼上您的 [連接字串]  ，然後按一下 [確定]  以連接 Azure Cosmos DB 帳戶。 如需擷取連接字串的資訊，請參閱[取得連接字串](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>以 URI 連線至 Azure Data Lake Store

如果您想要取得資源的存取權，而這些資源不存在於您的訂用帳戶中， 但他人授權給您取得資源的 URI。 在此情況下，您可以在登入後使用 URI 連線至 Data Lake Store。 請參閱下列步驟。

1. 開啟儲存體總管。
2. 在左窗格中，展開 [本機與已連結的資源]  。
3. 以滑鼠右鍵按一下 **Data Lake Store**，然後從內容功能表中選取 [連線至 Data Lake Store...]  。

    ![連線至 Data Lake Store 內容功能表](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 輸入 URI，工具即會瀏覽至您剛才輸入的 URL 位置。

    ![連線至 Data Lake Store 內容對話方塊](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![連線至 Data Lake Store 結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>在儲存體總管產生 SAS

### <a name="account-level-sas"></a>帳戶層級的 SAS

1. 以滑鼠右鍵按一下您要共用，然後選取 儲存體帳戶**取得共用存取簽章...** .

    ![取得 SAS 內容功能表選項][14]

2. 在 [產生共用存取簽章]  對話方塊中，指定您要用於此帳戶的時間範圍和權限，然後按一下 [建立]  按鈕。

    ![取得 SAS 對話方塊][15]

3. 您現在可以複製**連接字串**或原始**查詢字串**到剪貼簿。

### <a name="service-level-sas"></a>服務層級 SAS

[如何取得儲存體總管中的 blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶

如果您需要尋找儲存體資源，但不知道其所在位置，您可以使用左窗格頂端的搜尋方塊來搜尋此資源。

在搜尋方塊中輸入資料時，左窗格會顯示與您在當時為止已輸入的搜尋值相符的所有資源。 例如，下列螢幕擷取畫面會顯示針對**端點**所進行的搜尋：

![儲存體帳戶搜尋][23]

> [!NOTE]
> 使用 [帳戶管理面板]  來取消選取任何不包含您要搜尋之項目的訂用帳戶，以改善搜尋的執行時間。 您也可以在節點上按一下滑鼠右鍵，並選擇 [從這裡搜尋]  以從特定節點開始搜尋。
>
>

## <a name="next-steps"></a>後續步驟

* [使用儲存體總管來管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
* [在 Azure 儲存體總管 (預覽) 中管理 Azure Cosmos DB](./cosmos-db/storage-explorer.md)
* [使用儲存體總管來管理 Azure Data Lake Store 資源](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
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
