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
ms.openlocfilehash: 43e76b9331eb8dbe95265810b9191a10d4caee08
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272015"
---
# <a name="get-started-with-storage-explorer"></a>開始使用儲存體總管

## <a name="overview"></a>總覽

Microsoft Azure 儲存體總管是獨立應用程式，可讓您輕鬆地使用 Windows、macOS 和 Linux 上的 Azure 儲存體資料。 在本文中，您將瞭解幾種連接和管理 Azure 儲存體帳戶的方式。

![Microsoft Azure 儲存體總管][0]

## <a name="prerequisites"></a>必要條件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

下列版本的 Windows 支援儲存體總管：

* Windows 10 (建議採用)
* Windows 8
* Windows 7

針對所有版本的 Windows，需要 .NET Framework 4.6.2 或更新版本。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

下列 macOS 版本支援儲存體總管：

* macOS 10.12 "Sierra" 及更新版本

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

儲存體總管適用于最常見的 Linux 發行版本的[貼齊存放區](https://snapcraft.io/storage-explorer)，而且是建議的安裝方法。 當新版本發行至 [貼齊存放區] 時，[儲存體總管] 嵌入式管理單元會自動安裝其所有相依性和更新。

如需支援的發行版本清單，請移至[snapd 安裝頁面](https://snapcraft.io/docs/installing-snapd)。

儲存體總管需要使用密碼管理員，您可能必須先手動連接，儲存體總管才能正常運作。 您可以執行下列命令，將儲存體總管連接到系統的密碼管理員：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

儲存體總管也會以 gz 下載的形式提供，但您必須手動安裝相依性。 下列的 Linux 散發版本支援 gz 安裝：

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Gz 安裝可能適用于其他散發套件，但僅正式支援這些發行版本。

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
> 若要在登入之後完整存取資源，儲存體總管需要管理（Azure Resource Manager）和資料層許可權。 這表示您需要 Azure Active Directory （Azure AD）許可權，這可讓您存取儲存體帳戶、帳戶中的容器，以及容器中的資料。 如果您只有資料層的許可權，請考慮[透過 Azure AD 新增資源](#add-a-resource-via-azure-ad)。 如需儲存體總管需要之特定許可權的詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)。

1. 在儲存體總管中，選取 [**管理帳戶**] 以移至 [帳戶管理] 面板。

    ![管理帳戶][1]

2. 左窗格現在會顯示您已登入的所有 Azure 帳戶。 若要連線到另一個帳戶，請選取 [**新增帳戶**]。

3. 如果您想要登入國家雲端或 Azure Stack，請選取 [ **azure 環境**] 下拉式清單，選擇您想要使用的 azure 雲端。 選擇您的環境之後，請選取 [登**入**] 按鈕。 如需詳細資訊，請參閱[將儲存體總管連接到 Azure Stack 訂](/azure-stack/user/azure-stack-storage-connect-se)用帳戶。

    ![登入選項][2]

4. 使用 Azure 帳戶成功登入後，左窗格中會新增該帳戶以及與該帳戶相關聯的 Azure 訂用帳戶。 選取您想要使用的 Azure 訂用帳戶，然後選取 [套用]。 （選取 [**所有**訂用帳戶] 會在列出的所有 Azure 訂用帳戶之間切換您的選擇。）

    ![選取 Azure 訂用帳戶][3]

    左窗格會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。

    ![已選取的 Azure 訂用帳戶][4]

### <a name="attach-a-specific-resource"></a>附加特定資源

有多種方式可將連結至儲存體總管中的資源：

* 透過[Azure AD 新增資源](#add-a-resource-via-azure-ad)。 如果您只有資料層的許可權，請使用此選項來新增 blob 容器或 Azure Data Lake Storage Gen2 Blob 儲存體容器。
* [使用連接字串](#use-a-connection-string)。 如果您有儲存體帳戶的連接字串，請使用此選項。 儲存體總管同時支援金鑰和[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)連接字串。
* [使用 SAS URI](#use-a-sas-uri)。 如果您有 blob 容器、檔案共用、佇列或資料表的[SAS URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) ，請使用它來附加至資源。 若要取得 SAS URI，您可以使用[儲存體總管](#generate-a-sas-in-storage-explorer)或[Azure 入口網站](https://portal.azure.com)。
* [使用名稱和金鑰](#use-a-name-and-key)。 如果您知道儲存體帳戶的其中一個帳戶金鑰，您可以使用此選項來快速連接。 儲存體帳戶的金鑰位於[Azure 入口網站](https://portal.azure.com)中的 [儲存體帳戶**存取金鑰**] 面板上。
* [附加至本機模擬器](#attach-to-a-local-emulator)。 如果您使用其中一個可用的 Azure 儲存體模擬器，請使用此選項輕鬆地連接到您的模擬器。
* [使用連接字串連接到 Azure Cosmos DB 帳戶](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 如果您有 CosmosDB 實例的連接字串，請使用此選項。
* [依 URI 連接到 Azure Data Lake 存放區](#connect-to-azure-data-lake-store-by-uri)。 如果您有 Azure Data Lake 存放區的 URI，請使用此選項。

#### <a name="add-a-resource-via-azure-ad"></a>透過 Azure AD 新增資源

1. 選取左側垂直工具列上的 [**連接**] 按鈕，以開啟 [**連接**] 對話方塊：

    ![連接至 Azure 儲存體選項][9]

2. 如果您尚未這麼做，請使用 [**新增 Azure 帳戶**] 選項來登入可存取資源的 azure 帳戶。 登入之後，請**返回 [連線] 對話方塊**。

3. 選取 [透過**Azure Active Directory （Azure AD）新增資源**]，然後選取 **[下一步]** 。

4. 選取可存取您想要附加之儲存體資源的 Azure 帳戶和租使用者。 選取 [下一步]。

5. 選擇您想要附加的資源類型，然後輸入連接所需的資訊。 您在此頁面上輸入的資訊取決於您要新增的資源類型。 請務必選擇正確的資源類型。 輸入必要資訊之後，請選取 **[下一步]** 。

6. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果是，請選取 **[連線]** 。 否則，請使用 [**上一步**] 按鈕返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動移至代表連接的節點。 如果未移至該節點，請查看 [**本機 &**  > 連結的**儲存體帳戶** > （已連結的**容器）**  > ] [**Blob 容器**]。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-connection-string"></a>使用連接字串

1. 選取左側垂直工具列上的 [**連接**] 按鈕，以開啟 [**連接**] 對話方塊：

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用連接字串**]，然後選取 **[下一步]** 。

3. 為您的連線選擇 [顯示名稱]，然後輸入您的連接字串。 然後，選取 [下一步]。

4. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果是，請選取 **[連線]** 。 否則，請使用 [**上一步**] 按鈕返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動移至代表連接的節點。 如果不是移至該節點，請查看 [**本機 & 連接** > 的**儲存體帳戶**]。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-sas-uri"></a>使用 SAS URI

1. 選取左側垂直工具列上的 [**連接**] 按鈕，以開啟 [**連接**] 對話方塊：

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用共用存取簽章（SAS） URI**]，然後選取 **[下一步]** 。

3. 為您的連線選擇 [顯示名稱]，然後輸入您的 SAS URI。 您要附加之資源類型的服務端點應該自動填入。 如果您使用的是自訂端點，則可能不是。 選取 [下一步]。

4. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果是，請選取 **[連線]** 。 否則，請使用 [**上一步**] 按鈕返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動移至代表連接的節點。 如果未移至該節點，請在 [**本機 &**  > 連結的**儲存體帳戶** > （已連結的**容器）**  > ] 下，查看*您所附加容器類型的服務節點*。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-name-and-key"></a>使用名稱和金鑰

1. 選取左側垂直工具列上的 [**連接**] 按鈕，以開啟 [**連接**] 對話方塊：

    ![連接至 Azure 儲存體選項][9]

2. 選取 [**使用儲存體帳戶名稱和金鑰**]，然後選取 **[下一步]** 。

3. 選擇連接的顯示名稱。

4. 輸入您的儲存體帳戶名稱和它的其中一個存取金鑰。

5. 選擇要使用的**儲存體網域**，然後選取 **[下一步]** 。

6. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果是，請選取 **[連線]** 。 否則，請使用 [**上一步**] 按鈕返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動移至代表連接的節點。 如果不是移至該節點，請查看 [**本機 & 連接** > 的**儲存體帳戶**]。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="attach-to-a-local-emulator"></a>附加至本機模擬器

儲存體總管目前支援兩個官方儲存體模擬器：
* [Azure 儲存體模擬器](storage/common/storage-use-emulator.md)（僅限 Windows）
* [Azurite](https://github.com/azure/azurite)（Windows、macOS 或 Linux）

如果您的模擬器正在接聽預設埠，您可以使用 [**模擬器-預設埠**] 節點（在 [**本機 & 連接** > 的**儲存體帳戶**] 下找到）來快速存取您的模擬器。

如果您想要使用不同的名稱來連接，或如果您的模擬器不是在預設埠上執行，請遵循下列步驟：

1. 啟動您的模擬器。 當您這麼做時，請記下模擬器針對每個服務類型接聽的埠。

   > [!IMPORTANT]
   > 儲存體總管不會自動啟動您的模擬器。 您必須以手動方式啟動它。

2. 選取左側垂直工具列上的 [**連接**] 按鈕，以開啟 [**連接**] 對話方塊：

    ![連接至 Azure 儲存體選項][9]

3. 選取 [**附加至本機模擬器**]，然後選取 **[下一步]** 。

4. 為您的連線選擇 [顯示名稱]，並針對每個服務類型輸入模擬器正在接聽的埠。 文字方塊的開頭會是大部分模擬器的預設埠值。 [檔案**埠**] 方塊會保留空白，因為目前沒有任何一項官方模擬器支援檔案服務。 如果您所使用的模擬器支援檔案，您可以輸入正在使用的埠。 然後，選取 [下一步]。

5. 請檢查連線摘要，並確定所有資訊都正確無誤。 如果是，請選取 **[連線]** 。 否則，請使用 [**上一步**] 按鈕返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會自動移至代表連接的節點。 如果不是移至該節點，請查看 [**本機 & 連接** > 的**儲存體帳戶**]。 如果儲存體總管無法新增您的連線，或如果您在成功新增連線之後無法存取資料，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB 帳戶

除了透過 Azure 訂用帳戶管理 Azure Cosmos DB 帳戶以外，您也可以使用連接字串連接到 Azure Cosmos DB。 若要這樣做，請遵循下列步驟：

1. 在資源樹狀結構的左側，展開 [**本機] 和**[已附加]，以滑鼠右鍵按一下 [ **Azure Cosmos DB 帳戶**]，然後選取 **[連接到 Azure Cosmos DB]** 。

    ![使用連接字串連線到 Azure Cosmos DB][21]

2. 選取 [Azure Cosmos DB API]，輸入您的**連接字串**資料，然後選取 **[確定**] 以連接 Azure Cosmos DB 帳戶。 如需有關如何抓取連接字串的詳細資訊，請參閱[取得連接字串](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![連接字串][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>以 URI 連線至 Azure Data Lake Store

如果您想要存取不在訂用帳戶中的資源，您將需要可存取該資源的人員，以提供您資源 URI。 登入之後，您可以使用 URI 來連線到 Data Lake Store。 若要這樣做，請遵循下列步驟：

1. 開啟儲存體總管。
2. 在左窗格中，展開 [本機與已連結的資源]。
3. 以滑鼠右鍵按一下 [ **Data Lake Store**]。 從快捷方式功能表中，選取 **[連接到 Data Lake Store]** 。

    ![連接到 Data Lake Store 內容功能表](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 輸入 URI。 此工具會移至您剛才輸入的 URL 位置。

    ![[連接到 Data Lake Store 內容] 對話方塊](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![連接到 Data Lake Store 結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>在儲存體總管中產生 SAS

### <a name="account-level-sas"></a>帳戶層級 SAS

1. 以滑鼠右鍵按一下您要共用的儲存體帳戶，然後選取 [**取得共用存取**簽章]。

    ![取得 SAS 內容功能表選項][14]

2. 在 [**產生共用存取**簽章] 對話方塊中，指定您要用於帳戶的時間範圍和許可權，然後選取 [**建立**]。

    ![取得 SAS 對話方塊][15]

3. 您現在可以將**連接字串**或原始**查詢字串**複製到剪貼簿。

### <a name="service-level-sas"></a>服務層級 SAS

[如何在儲存體總管中取得 blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶

如果您需要尋找儲存體資源，而不知道其所在位置，可以使用左窗格頂端的 [搜尋] 方塊來搜尋資源。

在搜尋方塊中輸入資料時，左窗格會顯示與您在當時為止已輸入的搜尋值相符的所有資源。 例如，下列螢幕擷取畫面會顯示針對**端點**所進行的搜尋：

![儲存體帳戶搜尋][23]

> [!NOTE]
> 若要加快搜尋速度，請使用 [帳戶管理] 面板取消選取任何不包含您要搜尋之專案的訂用帳戶。 您也可以在節點上按一下滑鼠右鍵，然後選取 [**從這裡搜尋**]，開始從特定節點進行搜尋。
>
>

## <a name="next-steps"></a>後續步驟

* [使用儲存體總管管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
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
