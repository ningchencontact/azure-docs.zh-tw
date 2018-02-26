---
title: "在 Azure 儲存體總管中管理 Azure Data Lake Store 資源"
description: "可讓使用者在 Azure 儲存體總管中存取和管理您的 ADLS 資料和資源"
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>使用儲存體總管來管理 Azure Data Lake Store 資源 (預覽)

[Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) 是用來儲存大量非結構化資料 (例如文字或二進位資料) 的服務。 使用者可透過 HTTP 或 HTTPS 從任何位置存取資料。 Azure 儲存體總管中的 ADLS 可讓使用者存取和管理 ADLS 資料和資源，以及其他如 Blob 和佇列之類的 Azure 實體。 現在，使用者可以使用同一項工具在同一處管理其不同的 Azure 實體。

另一個優點是，使用者不需擁有訂用帳戶權限即可管理 ADLS 資料。 在儲存體總管中，只要他人授與權限，使用者即可將 ADLS 路徑連結至「本機與已連結的資源」節點。

## <a name="prerequisites"></a>先決條件
若要完成本文中的步驟，您必須符合下列先決條件︰

*   Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial)。
*   Azure Data Lake Store 帳戶。 如需關於如何建立帳戶的指示，請參閱[開始使用 Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="installation"></a>安裝

在這裡安裝最新的 Azure 儲存體總管位元：[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。 我們現在支援 Windows、Linux 及 MAC 版本。

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

1. 在安裝 **Azure 儲存體總管**之後，按一下左邊的**外掛程式**圖示，如下圖所示。
       
   ![外掛程式圖示](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. 選取 [新增 Azure 帳戶]，然後按一下 [登入]。

   ![連線到 Azure 訂用帳戶](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. 在 [Azure 登入] 對話方塊中，選取 [登入]，然後輸入您的 Azure 認證。

    ![登入](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. 從清單中選取您的訂用帳戶，然後按一下 [套用]。

    ![套用](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    [總管] 窗格會更新，並顯示選取之訂閱中的帳戶。

    ![帳戶清單](./media/data-lake-store-in-storage-explorer/account-list.png)

    您已成功將 **Azure Data Lake Store** 連線至 Azure 訂用帳戶。

## <a name="connect-to-data-lake-store"></a>連線至 Data Lake Store
如果您想要取得資源的存取權，而這些資源不存在於您的訂用帳戶中， 但他人授權給您取得資源的 URI。 在此情況下，您可以在登入後使用 URI 連線至 Data Lake Store。 請參閱下列步驟。
1. 開啟儲存體 Explorer (預覽)。
2. 在左窗格中，展開 [本機與已連結的資源]。
3. 以滑鼠右鍵按一下 **Data Lake Store**，然後從內容功能表中選取 [連線至 Data Lake Store...]。

      ![連線至 Data Lake Store 內容功能表](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 輸入 URI，工具即會瀏覽至您剛才輸入的 URL 位置。

      ![連線至 Data Lake Store 內容對話方塊](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![連線至 Data Lake Store 結果](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>檢視 Azure Data Lake Store 帳戶的內容
Azure Data Lake Store 帳戶的資源包含資料夾和檔案。

下列步驟說明如何在儲存體總管 (預覽) 中檢視 ADLS 帳戶的內容：

1. 開啟儲存體 Explorer (預覽)。
2. 在左窗格中，展開您要檢視的 Azure Data Lake Store 帳戶所屬的訂用帳戶。
3. 展開 **Data Lake Store**。
4. 以滑鼠右鍵按一下您要檢視的 Azure Data Lake Store 帳戶節點，然後從內容功能表中選取 [開啟]。 您也可以按兩下要開啟的 ADLS 帳戶。 
5. 主窗格會顯示 ADLS 帳戶的內容。

     ![主窗格](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Azure Data Lake Store 資源管理

您可以透過下列操作來管理 Azure Data Lake Store 資源：
*   瀏覽多個 ADL 帳戶的 ADLS 資源。  
*   使用連接字串直接連線和管理 ADLS。 
*   透過 [本機與已連結的資源] 下的 ACL 檢視與他人共用的 ADLS 資源。
*   執行檔案/資料夾 CRUD 作業：支援遞迴資料夾和複選的檔案。 
*   將資料夾拖曳、放置和新增至快速存取和最近的位置；其體驗類似於桌面檔案總管。 
*   在儲存體總管中按一下，即可複製和開啟 ADL 超連結。 
*   在右下方的窗格中顯示活動記錄，以檢視活動狀態。
*   顯示資料夾統計資料和檔案屬性。

## <a name="manage-resources-in-azure-storage-explorer"></a>在 Azure 儲存體總管中管理資源
建立 Azure Data Lake Store 帳戶之後，您可以上傳資料夾和檔案，以及在本機電腦上下載和開啟資源。 此外，您可以釘選至快速存取、新增資料夾、複製 URL 及全選。 此外，您也能夠複製、貼上、重新命名、刪除、顯示資料夾統計資料、重新整理。

下列項目說明如何管理 Azure Data Lake Store 帳戶內的資源。 根據您想要執行的工作遵循下列步驟︰
   * **上傳檔案**

     1. 在主窗格工具列上，選取 [上傳]，然後從下拉式功能表中選取 [上傳檔案...]。

        ![上傳檔案功能表](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. 在 [選取要上傳的檔案] 對話方塊中，選取您要上傳的檔案。

        ![上傳資料夾對話方塊](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. 選取 [開啟] 以開始上傳。
   * **上傳資料夾**

     1. 在主窗格工具列上，選取 [上傳]，然後從下拉式功能表中選取 [上傳資料夾...]。

        ![上傳資料夾功能表](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. 在 [選取要上傳的資料夾] 對話方塊中，選取您要上傳的資料夾。

        ![上傳資料夾對話方塊](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. 選取 [選取資料夾] 以開始上傳資料夾。

        ![上傳資料夾對話方塊](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > 您也可以直接拖曳本機電腦中的檔案和資料夾，以實作上傳。 
       
   * **將資料夾或檔案下載到本機電腦**

     1. 選取您要下載的資料夾或檔案。
     2. 在主窗格工具列上選取 [下載] 。
     3. 在 [選取要將下載的檔案儲存到哪個資料夾] 對話方塊中，指定要下載資料夾或檔案的位置。 並指定您要提供給它的名稱。
     4. 選取 [ **儲存**]。
   * **從本機電腦開啟資料夾或檔案**

     1. 選取您要開啟的資料夾或檔案。
     2. 在主窗格的工具列上選取 [開啟]，或以滑鼠右鍵按一下選取的資料夾或檔案，然後在內容功能表中按一下 [開啟]。
     3. 檔案會進行下載，並使用與檔案的基礎檔案類型相關聯的應用程式開啟。 或者，資料夾會在主窗格中開啟。

        ![開啟檔案](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **將資料夾或檔案複製到剪貼簿**

     1. 選取您要複製的資料夾或檔案。
     2. 在主窗格的工具列上選取 [複製]，或以滑鼠右鍵按一下選取的資料夾或檔案，然後在內容功能表中按一下 [複製]。
     3. 在左窗格中，瀏覽至另一個 ADLS 帳戶，然後按兩下在主窗格中加以檢視。
     4. 在主窗格工具列上選取 [貼上]，以建立複本。 或者，在目的地的內容功能表中按一下 [貼上]。

        ![複製並貼上資料夾或檔案](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + 跨儲存體類型的「複製-貼上」**不受**支援。 您可以複製 ADLS 資料夾或檔案，並貼到另一個 ADLS 帳戶。 但是，您**無法**複製 ADLS 資料夾或檔案然後貼到 Blob 儲存體，反之亦然。 
          > + 「複製-貼上」的運作方式，是先將資料夾或檔案下載到本機，然後再上傳至目的地。 此工具**不會**在後端執行動作。 大型檔案的「複製-貼上」速度很慢。 目前，高效能檔案的「複製-移動」正在進行最佳化。
   * **刪除資料夾或檔案**

     1. 選取您要刪除的資料夾或檔案。
     2. 在主窗格的工具列上選取 [刪除]，或以滑鼠右鍵按一下選取的資料夾或檔案，然後在內容功能表中按一下 [刪除]。
     3. 選取確認對話方塊上的 [是]。

        ![複製並貼上資料夾或檔案](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **釘選到快速存取**

     1. 選取您要釘選的資料夾。
     2. 在主窗格的工具列上，選取 [釘選到快速存取]。
     3. 在左窗格中，您會看到選取的資料夾已新增至 [快速存取] 節點。

        ![釘選到快速存取](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. 建立快速存取之後，您即可輕鬆存取資源。
   * **深層連結**
     1. 如果您有 URL，您可以直接將 URL 輸入至**檔案總管**或瀏覽器中的位址路徑。
     2. 然後，**Storage Explorer.exe** 會自動啟動並瀏覽至您剛才輸入的 URL 位置。

        ![檔案總管中的深層連結](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>後續步驟
* 檢視 [最新的儲存體 Explorer (預覽) 版本資訊與影片](http://www.storageexplorer.com)。
* 了解如何[在 Azure 儲存體總管中管理 Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)。
* 在[開始使用儲存體總管 (預覽)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer) 中深入了解儲存體總管。
* 開始使用 Azure Data Lake Store (ADLS)[Azure Data Lake Store 概觀](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)
* 觀看下列影片，以了解如何[在 Azure 儲存體總管中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
