---
title: 使用 Azure 儲存體總管來管理 Azure 檔案共用
description: 了解如何使用 Azure 儲存體總管來管理 Azure 檔案服務。
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>使用 Azure 儲存體總管來管理 Azure 檔案共用 
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 本指南會逐步說明透過 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來使用 Azure 檔案共用的基本概念。 Azure 儲存體總管是 Windows、macOS 和 Linux 適用的熱門用戶端工具，用於管理 Azure 檔案共用和其他儲存體資源。

本快速入門需要安裝 Azure 儲存體總管。 如果您需要進行安裝，請瀏覽 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)進行下載。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立資源群組和儲存體帳戶
> * 建立 Azure 檔案共用 
> * 建立目錄
> * 上傳檔案
> * 下載檔案
> * 建立及使用共用快照集

如果您沒有 Azure 訂用帳戶，您可以在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-storage-account"></a>建立儲存體帳戶
Azure 儲存體總管無法建立新的資源，所以為了此示範的目的，請使用 [Azure 入口網站](https://portal.azure.com/)建立儲存體帳戶。 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>將 Azure 儲存體總管連線至 Azure 資源
第一次啟動時，會顯示 [Microsoft Azure 儲存體總管 - 連線] 視窗。 Azure 儲存體總管提供數種方式來連線至儲存體帳戶： 

- **透過您的 Azure 帳戶登入**：可讓您以您組織或 Microsoft 帳戶的使用者認證登入。 
- **透過連接字串或 SAS 權杖連線到特定儲存體帳戶**：連接字串是一個包含儲存體帳戶名稱和儲存體帳戶金鑰/SAS 權杖的特殊字串，可讓 Azure 儲存體總管直接存取儲存體帳戶 (而非僅只查看 Azure 帳戶內的所有儲存體帳戶)。 若要深入了解連接字串，請參閱[設定 Azure 儲存體連接字串](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
- **使用儲存體帳戶名稱和金鑰來連線到特定儲存體帳戶**：使用儲存體帳戶的儲存體名稱和儲存體帳戶金鑰來連線到 Azure 儲存體。

基於本快速入門的目的，請使用您的 Azure 帳戶登入。 選取 [新增 Azure 帳戶]，然後按一下 [登入]。 遵循螢幕上的提示來登入 Azure 帳戶。

![[Microsoft Azure 儲存體總管 - 連線] 視窗](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>建立檔案共用
若要在 storageacct<random number> 儲存體帳戶內建立第一個 Azure 檔案共用：

1. 選取您所建立的儲存體帳戶。
2. 以滑鼠右鍵按一下 [檔案共用]，然後選取 [建立檔案共用]。  
    ![環境中檔案共用資料夾和操作功能表的螢幕擷取畫面](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. 輸入 myshare 作為檔案共用，然後按 **Enter**。

> [!Important]  
> 共用名稱必須全部使用小寫字母、數字和單一連字號，但開頭不可以是連字號。 如需有關為檔案共用與檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案及中繼資料](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

建立檔案共用之後，右窗格會為您的檔案共用開啟一個索引標籤。 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>處理 Azure 檔案共用的內容
您現在已建立 Azure 檔案共用，並且可使用 SMB 將檔案共用掛接在 [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md) 或 [macOS](storage-how-to-use-files-mac.md) 上。 或者，您可以使用 Azure 入口網站來處理 Azure 檔案共用。 透過 Azure 入口網站提出的所有要求都是使用 REST API 進行，讓您不需 SMB 存取權，即可在用戶端上建立、修改及刪除檔案和目錄。

### <a name="create-a-directory"></a>建立目錄
新增目錄可提供一個階層式結構，以便 Azure 檔案共用。 您可以建立多個層級，但您必須先確定所有父目錄都存在，才能建立子目錄。 例如，對於 myDirectory/mySubDirectory 路徑，您必須先建立 myDirectory 目錄，然後再建立 mySubDirectory 目錄。 

1. 在檔案共用索引標籤上，按一下於上方功能表的 [+ 新資料夾] 按鈕。 [建立新目錄] 頁面將會開啟。
    ![環境中新增資料夾按鈕的螢幕擷取畫面](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. 輸入 myDirectory 作為名稱，然後按一下 [確定]。 

myDirectory 目錄會列在 myshare 檔案共用的索引標籤上。

### <a name="upload-a-file"></a>上傳檔案 
將檔案從本機電腦上傳到您的檔案共用中的新目錄。 您可以上傳整個資料夾或只上傳單一檔案。

1. 在頂端的功能表中，選取 [上傳]。 這可讓您選擇上傳資料夾或檔案。

2. 選取 [上傳檔案]，然後從本機電腦選擇要上傳的檔案。

3. 在 [上傳至目錄] 中輸入 myDirectory，然後按一下 [上傳]。 

完成時，檔案應會出現在 [myDirectory] 頁面的清單中。

### <a name="download-a-file"></a>下載檔案
您可以在檔案上按一下滑鼠右鍵並選取 [下載]，以下載您檔案共用中的一份檔案。 選擇您在本機電腦上要放置檔案的位置，然後按一下 [儲存]。

您會在視窗底部的 [活動] 窗格中看到下載進度。

## <a name="create-and-modify-share-snapshots"></a>建立及修改共用快照集
快照集會保留 Azure 檔案共用的時間點複本。 檔案共用快照集類似於您可能已經熟悉的其他技術，例如：
- Windows 檔案系統的[磁碟區陰影複製服務 (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)，例如 NTFS 和 ReFS。
- Linux 系統的[邏輯磁碟區管理員 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照集
- macOS 的 [Apple 檔案系統 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照集。 

若要建立共用快照集：

1. 開啟 myshare 檔案共用的索引標籤。
2. 在索引標籤頂端的功能表中，按一下 [建立快照集] (視 Azure 儲存體總管的視窗維度而定，這可能會隱藏在 [更多...] 後面)。  
    ![環境中建立快照集按鈕的螢幕擷取畫面](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>列出並瀏覽共用快照集
建立快照集之後，您可以按一下 [檢視檔案共用的快照集] (視 Azure 儲存體總管的視窗維度而定，這可能會隱藏在[更多...] 後面，以列出共用的快照集。 按兩下共用快照集即可加以瀏覽。

![瀏覽快照集畫面的螢幕擷取畫面](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>從共用快照集還原
為了示範如何從共用快照集還原檔案，我們必須先從即時的 Azure 檔案共用中刪除檔案。 瀏覽到 *myDirectory* 資料夾中，以滑鼠右鍵按一下您上傳的檔案，然後按一下 [刪除]。 然後，若要從共用快照集還原該檔案：

1. 按一下 [檢視檔案共用的快照集] (視 Azure 儲存體總管的視窗維度而定，這可能會隱藏在[更多...] 後面)。
2. 從清單中選取共用快照集，並按兩下進行瀏覽。
3. 瀏覽快照集，直到您找到您所刪除的檔案，請加以選取，然後按一下 [還原快照集] (視 Azure 儲存體總管的視窗維度而定，這可能會隱藏在[更多...] 後面)。 您會收到一則警告，表示還原檔案將會覆寫檔案共用的內容，而且無法復原。 選取 [確定] 。
4. 檔案現在應該位於它在即時 Azure 檔案共用之下的原始位置。

### <a name="delete-a-share-snapshot"></a>刪除共用快照集
若要刪除共用快照集，請[瀏覽至共用快照集的清單](#list-and-browse-share-snapshots)。 以滑鼠右鍵按一下您要刪除的共用快照集，然後選取 [刪除]。

## <a name="clean-up-resources"></a>清除資源
Azure 儲存體總管無法移除資源；您可以使用 [Azure 入口網站](https://portal.azure.com/)從本快速入門清除。 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>後續步驟
- [使用 Azure 入口網站管理檔案共用](storage-how-to-use-files-portal.md)
- [使用 Azure PowerShell 管理檔案共用](storage-how-to-use-files-powershell.md)
- [使用 Azure CLI 管理檔案共用](storage-how-to-use-files-cli.md)
- [規劃 Azure 檔案部署](storage-files-planning.md)