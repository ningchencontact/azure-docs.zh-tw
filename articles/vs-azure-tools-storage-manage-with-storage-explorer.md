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
ms.openlocfilehash: c4aad2f2f5bca25ead03518b2de9ac9315172052
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934752"
---
# <a name="get-started-with-storage-explorer"></a>開始使用儲存體總管

## <a name="overview"></a>總覽

Microsoft Azure 儲存體總管是獨立應用程式，可讓您輕鬆地使用 Windows、macOS 和 Linux 上的 Azure 儲存體資料。 在本文中，您將了解連線及管理 Azure 儲存體帳戶的數種方式。

![Microsoft Azure 儲存體總管][0]

## <a name="prerequisites"></a>必要條件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

下列版本的 Windows 支援儲存體總管：

* Windows 10 (建議採用)
* Windows 8
* Windows 7

所有 Windows 版本都需要 .NET Framework 4.6.2 或更新版本。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

下列 macOS 版本支援儲存體總管：

* macOS 10.12 "Sierra" 及更新版本

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

儲存體總管適用于最常見的 Linux 發行版本的[貼齊存放區](https://snapcraft.io/storage-explorer)，而且是建議的安裝方法。 [儲存體總管] 嵌入式管理單元會在新版本發行至 [貼齊存放區] 時，自動安裝其所有相依性和更新。

如需支援的發行版本清單，請造訪[snapd 安裝頁面](https://snapcraft.io/docs/installing-snapd)。

儲存體總管需要使用密碼管理員，這可能需要手動連接，儲存體總管才能正常運作。 您可以使用下列命令，將儲存體總管連接到系統的密碼管理員：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

儲存體總管也會以 gz 下載的形式提供，但您必須手動安裝相依性。 下列的 Linux 散發版本支援 gz 安裝：

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Gz 安裝可能會在其他散發套件上執行，但只會正式支援上面列出的版本。

如需在 Linux 上安裝儲存體總管的詳細說明，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

---

## <a name="download-and-install"></a>下載並安裝

[下載並安裝儲存體總管](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務

儲存體總管提供數種方式來連線至儲存體帳戶。 一般來說，您可以：

* [登入 Azure 以存取您的訂用帳戶及其資源](#sign-in-to-azure)
* [附加特定的儲存體或 CosmosDB 資源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登入 Azure

> [!NOTE]
> 若要在登入之後完整存取資源，儲存體總管需要管理（ARM）和資料層許可權。 這表示您需要 Azure AD 許可權，讓您能夠存取儲存體帳戶、帳戶中的容器，以及容器中的資料。 如果您只有資料層的許可權，請考慮使用 [[附加于 Azure AD](#add-a-resource-via-azure-ad)]。 如需儲存體總管需要之確切許可權的詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)。

1. 在儲存體總管中，選取 [管理帳戶] 以移至 [帳戶管理面板]。

    ![管理帳戶][1]

2. 左窗格現在會顯示您已登入的所有 Azure 帳戶。 若要連線到另一個帳戶，請選取 [新增帳戶]

3. 如果您要登入國家雲端或 Azure Stack，請按一下 [Azure 環境] 下拉式清單以選取您要使用的 Azure 雲端。 選擇您的環境之後，請按一下 [登**入**] 按鈕。 如需詳細資訊，請參閱[將儲存體總管連接到 Azure Stack 訂](/azure-stack/user/azure-stack-storage-connect-se)用帳戶。

    ![登入選項][2]

4. 使用 Azure 帳戶成功登入後，左窗格中會新增該帳戶以及與該帳戶相關聯的 Azure 訂用帳戶。 選取您要使用的 Azure 訂用帳戶，然後選取 [套用] \(選取 [所有訂用帳戶:] 切換方塊，可選取全部或不選取任何列出的 Azure 訂用帳戶)。

    ![選取 Azure 訂用帳戶][3]

    左窗格會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。

    ![已選取的 Azure 訂用帳戶][4]

### <a name="attach-a-specific-resource"></a>附加特定資源

您可以使用不同的選項，附加至儲存體總管中的資源：

* 透過[Azure AD 新增資源](#add-a-resource-via-azure-ad)：如果您只有資料層的許可權，則可以使用此選項來新增 Blob 容器或 ADLS Gen2 Blob 容器。
* [使用連接字串](#use-a-connection-string)：如果您有儲存體帳戶的連接字串，則為。 儲存體總管同時支援金鑰和[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)連接字串。
* [使用 SAS URI](#use-a-sas-uri)：如果您有 Blob 容器、檔案共用、佇列或資料表的[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI。 若要取得 SAS URI，您可以使用[儲存體總管](#generate-a-sas-in-storage-explorer)或[Azure 入口網站](https://portal.azure.com)。
* [使用名稱和金鑰](#use-a-name-and-key)：如果您知道儲存體帳戶的其中一個帳戶金鑰，您可以使用此選項來快速連接。 儲存體帳戶的金鑰位於[Azure 入口網站](https://portal.azure.com)的儲存體帳戶**存取金鑰**面板上。
* [附加至本機模擬器](#attach-to-a-local-emulator)：如果您使用其中一個可用的 Azure 儲存體模擬器，請使用此選項輕鬆地連接到您的模擬器。
* [使用連接字串連線到 Azure Cosmos DB 帳戶](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)：如果您有 CosmosDB 實例的連接字串，則為。
* [依 URI 連接到 Azure Data Lake 存放區](#connect-to-azure-data-lake-store-by-uri)：如果您有 Azure Data Lake 存放區的 URI。

#### <a name="add-a-resource-via-azure-ad"></a>透過 Azure AD 新增資源

1. 按一下左側的 [**連接] 按鈕**（垂直工具列），以開啟 [**連接] 對話方塊**。

    ![連接至 Azure 儲存體選項][9]

2. 如果您尚未這麼做，請使用 [**新增 Azure 帳戶**] 選項來登入可存取資源的 azure 帳戶。 登入之後，請返回 [**連接] 對話方塊**。

3. 選取 [透過**Azure Active Directory （Azure AD）新增資源**] 選項，然後按 **[下一步]** 。

4. 選取可存取您想要附加之儲存體資源的 Azure 帳戶和租使用者。 按一下 [下一步]。

5. 選擇您想要附加的資源類型，然後輸入連接所需的資訊。 此頁面上的輸入會根據您要新增的資源類型而變更。 請務必選擇正確的資源類型。 填入所需的資訊之後，請按 **[下一步]** 。

6. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果所有資訊看起來都正確，請按一下 **[連接]** 。 否則，請使用 [**上一步**] 按鈕返回前一頁，以更正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動流覽至代表連線的節點。 您也可以查看 [**本機 &** ] → [**儲存體帳戶**→ **（已附加的容器）** ] → [ **Blob 容器**] （如果有的話）。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取您的資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以取得說明。

#### <a name="use-a-connection-string"></a>使用連接字串

1. 按一下左側的 [**連接] 按鈕**（垂直工具列），以開啟 [**連接] 對話方塊**。

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用連接字串**] 選項，然後按 **[下一步]** 。

3. 選擇連接的顯示名稱，然後在您的連接字串中輸入。 然後按 [下一步]。

4. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果所有資訊看起來都正確，請按一下 **[連接]** ，否則請使用 [**上一步**] 按鈕返回前一頁，以更正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動流覽至代表連線的節點。 如果基於某些原因，您也可以在 [已**附加的本機 &** →**儲存體帳戶**] 下查看。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取您的資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以取得說明。

#### <a name="use-a-sas-uri"></a>使用 SAS URI

1. 按一下左側的 [**連接] 按鈕**（垂直工具列），以開啟 [**連接] 對話方塊**。

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用共用存取簽章（SAS） URI** ] 選項，然後按 **[下一步]** 。

3. 為您的連線選擇 [顯示名稱]，然後在您的 SAS URI 中輸入。 您要附加之資源類型的服務端點應該自動填入。 如果您使用的是自訂端點，則可能不是。 按一下 [下一步]。

4. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果所有資訊看起來都正確，請按一下 **[連接]** ，否則請使用 [**上一步**] 按鈕返回前一頁以更正任何錯誤的資訊。

成功新增連接之後，資源樹狀結構會自動流覽至代表連線的節點。 您也可以在 [**本機 & 連接**] → [**儲存體帳戶**→（已連結的**容器）** ] 下，→**您附加的容器類型的服務節點**（如果有的話）。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取您的資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以取得說明。

#### <a name="use-a-name-and-key"></a>使用名稱和金鑰

1. 按一下左側的 [**連接] 按鈕**（垂直工具列），以開啟 [**連接] 對話方塊**。

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用儲存體帳戶名稱和金鑰**] 選項，然後按 **[下一步]** 。

3. 選擇連接的顯示名稱。

4. 輸入您的儲存體帳戶名稱和它的其中一個存取金鑰。

5. 選擇要使用的**儲存體網域**，然後按 **[下一步]** 。

4. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果所有資訊看起來都正確，請按一下 **[連接]** ，否則請使用 [**上一步**] 按鈕返回前一頁，以更正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動流覽至代表連線的節點。 如果基於某些原因，您也可以在 [已**附加的本機 &** →**儲存體帳戶**] 下查看。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取您的資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以取得說明。

#### <a name="attach-to-a-local-emulator"></a>附加至本機模擬器

儲存體總管目前支援兩個官方儲存體模擬器：
* [Azure 儲存體模擬器](storage/common/storage-use-emulator.md)（僅限 Windows）
* [Azurite](https://github.com/azure/azurite)（Windows、macOS 或 Linux）

如果您的模擬器正在接聽預設埠，您可以使用 [**模擬器-預設埠**] 節點（在 [**本機 &** ] 底下連結→ [**儲存體帳戶**]）來快速存取您的模擬器。

如果您想要使用不同的名稱來連接，或如果您的模擬器不是在預設埠上執行：

1. 啟動您的模擬器。 當您這麼做時，請記下模擬器針對每個服務類型接聽的埠。

   > [!IMPORTANT]
   > 儲存體總管不會自動啟動您的模擬器。 您必須自行加以啟動。

2. 按一下左側的 [**連接] 按鈕**（垂直工具列），以開啟 [**連接] 對話方塊**。

    ![連接至 Azure 儲存體選項][9]

3. 選取 [**附加至本機模擬器**] 選項，然後按 **[下一步]** 。

4. 為您的連線選擇 [顯示名稱]，並輸入您的模擬器在每個服務類型上接聽的埠。 文字方塊的開頭會是大部分模擬器的預設埠值。 檔案**埠**會保留空白，因為目前沒有任何一項官方模擬器支援檔案服務。 如果您所使用的模擬器支援檔案，則您可以輸入正在使用的通訊埠。 按一下 [下一步]。

5. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果所有資訊看起來都正確，請按一下 **[連接]** ，否則請使用 [上一**步**] 按鈕返回前一頁以更正任何錯誤的資訊。

成功新增連接之後，資源樹狀結構會自動流覽至代表連線的節點。 如果基於某些原因，您也可以在 [已**附加的本機 &** →**儲存體帳戶**] 下查看。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取您的資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以取得說明。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB 帳戶

除了透過 Azure 訂用帳戶管理 Azure Cosmos DB 帳戶，另一種連線到 Azure Cosmos DB 的方法是使用連接字串。 使用下列步驟來使用連接字串進行連線。

1. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)，以滑鼠右鍵按一下 [Azure Cosmos DB 帳戶]，選擇 [連線到 Azure Cosmos DB...]

    ![透過連接字串連線到 Azure Cosmos DB][21]

2. 選擇 Azure Cosmos DB API，貼上您的 [連接字串]，然後按一下 [確定] 以連接 Azure Cosmos DB 帳戶。 如需擷取連接字串的資訊，請參閱[取得連接字串](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>以 URI 連線至 Azure Data Lake Store

如果您需要存取不在訂用帳戶中的資源，您將需要具有存取權的人員提供資源 URI。 登入之後，您可以依照下列步驟，使用 URI 來連線到 Data Lake Store：

1. 開啟儲存體總管。
2. 在左窗格中，展開 [本機與已連結的資源]。
3. 以滑鼠右鍵按一下 **Data Lake Store**，然後從內容功能表中選取 [連線至 Data Lake Store...]。

    ![連線至 Data Lake Store 內容功能表](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 輸入 URI，工具即會瀏覽至您剛才輸入的 URL 位置。

    ![連線至 Data Lake Store 內容對話方塊](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![連線至 Data Lake Store 結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>在儲存體總管中產生 SAS

### <a name="account-level-sas"></a>帳戶層級 SAS

1. 以滑鼠右鍵按一下您要共用的儲存體帳戶，然後選取 [**取得共用存取**簽章 ...]。

    ![取得 SAS 內容功能表選項][14]

2. 在 [**產生共用存取**簽章] 對話方塊中，指定您要用於帳戶的時間範圍和許可權。 按一下 [建立]。

    ![取得 SAS 對話方塊][15]

3. 您現在可以將**連接字串**或原始**查詢字串**複製到剪貼簿。

### <a name="service-level-sas"></a>服務層級 SAS

[如何在儲存體總管中取得 blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶

如果您需要尋找儲存體資源，而不知道其所在位置，可以使用左窗格頂端的 [搜尋] 方塊來搜尋資源。

在搜尋方塊中輸入資料時，左窗格會顯示與您在當時為止已輸入的搜尋值相符的所有資源。 例如，下列螢幕擷取畫面會顯示針對**端點**所進行的搜尋：

![儲存體帳戶搜尋][23]

> [!NOTE]
> 使用 [帳戶管理面板] 來取消選取任何不包含您要搜尋之項目的訂用帳戶，以改善搜尋的執行時間。 您也可以在節點上按一下滑鼠右鍵，並選擇 [從這裡搜尋] 以從特定節點開始搜尋。
>
>

## <a name="next-steps"></a>後續步驟

* [使用儲存體總管來管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
* [在儲存體總管中管理 Azure Cosmos DB （預覽）](./cosmos-db/storage-explorer.md)
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
