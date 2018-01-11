---
title: "在內部部署資料移轉至 Azure 儲存體與 AzCopy |Microsoft 文件"
description: "使用 AzCopy，將資料移轉，或將資料複製到，或從 blob、 資料表和檔案內容。 輕鬆地將資料從您的本機儲存體移轉到 Azure 儲存體。"
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: f2c0b80248ef706394b3f84df4c3da26fb79026a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>在內部部署資料移轉至雲端儲存體，使用 AzCopy

AzCopy 是設計用來複製資料，從 Microsoft Azure Blob、 檔案和資料表儲存體的命令列公用程式，使用簡單的命令，以獲得最佳效能所設計。 您可以複製的檔案系統和儲存體帳戶，或儲存體帳戶之間的資料。  

有兩個版本，您可以下載 AzCopy:

* [在 Linux 上的 AzCopy](storage-use-azcopy.md)建置以.NET Core Framework 提供 POSIX 樣式命令列選項的 Linux 平台為目標。 
* [AzCopy on Windows](../storage-use-azcopy.md) 內建有 .NET Framework，並且提供 Windows 樣式的命令列選項。 
 
在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立儲存體帳戶 
> * 使用 AzCopy 上傳您的資料
> * 修改的資料進行測試
> * 建立排程的工作或 cron 工作來識別要上傳新的檔案

## <a name="prerequisites"></a>必要條件

若要完成本教學課程： 

* 下載最新版的 AzCopy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy)或[Windows](http://aka.ms/downloadazcopy)。 

## <a name="log-in-to-the-azure-portalhttpsportalazurecom"></a>登入 [Azure 入口網站](https://portal.azure.com/)。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>如果您想要可以從次要區域的 blob 下載到您的本機儲存體和反之亦然，設定**複寫**至**讀取-存取-地理備援儲存體**。 選取此選項會建立[地理備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage)帳戶。 
>
>

## <a name="create-a-container"></a>建立容器

Blob 一律會上傳到容器中。 容器可讓您組織的 blob 的群組，像是您組織您在資料夾中的電腦上的檔案。 

請遵循下列步驟來建立容器：

1. 選取**儲存體帳戶**從主頁面中，按鈕，然後選取您建立的儲存體帳戶。
2. 選取**Blob**下**服務**，然後選取**容器**。 

![建立容器](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
容器名稱必須以字母或數字開頭，並且只能包含字母、數字和連字號 (-) 字元。 如需 Blob 和容器的其他命名規則，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>將資料夾中的所有檔案上都傳至 Blob 儲存體

您可以使用 AzCopy，以在上傳至 Blob 儲存體資料夾中的所有檔案[Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)或[Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download)。 若要上傳資料夾中的所有 blob，請輸入下列的 AzCopy 命令：

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

取代**\<金鑰\>**與您的帳戶金鑰。 在 Azure 入口網站中，您可以擷取您的帳戶金鑰選取**存取金鑰**儲存體帳戶設定 之下。 選取索引鍵，並將它貼到 AzCopy 命令。 如果指定的目的地容器不存在，則 AzCopy 會建立此容器並將檔案上傳至該容器中。 來源路徑更新為您的資料目錄，並取代**myaccount**中儲存體帳戶名稱的目的地 URL。

指定`--recursive`和`/S`上的選項，Linux 和 Windows 分別上傳至 Blob 儲存體以遞迴方式的指定目錄的內容。 當您執行 AzCopy 與其中一個選項時，所有子資料夾及檔案上傳以及。

## <a name="upload-modified-files-to-blob-storage"></a>修改檔案上傳至 Blob 儲存體
您可以使用 AzCopy[將檔案上傳](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features)根據其上次修改時間。 若要這麼做，修改或測試用途的來源目錄中建立新的檔案。 若要上傳僅更新或新檔案，加入`--exclude-older`或`/XO`AzCopy Linux 和 Windows 參數分別命令。

如果您只想要複製不存在於目的地的來源資源，同時指定兩者`--exclude-older`和`--exclude-newer`或`/XO`和`/XN`AzCopy Linux 和 Windows 中的參數分別命令。 AzCopy 上傳只更新的資料依時間戳記。
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>建立排程的工作或 cron 作業 
您可以建立排程工作/cron 作業執行的 AzCopy 命令指令碼的識別，並將新的內部部署資料上傳至雲端儲存體在特定時間間隔。 

複製到文字編輯器的 AzCopy 命令。 更新為適當的值的 AzCopy 命令的參數值。 將檔案儲存為`script.sh`或`script.bat`讓 Linux 及 Windows 上的 AzCopy 分別。 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy 會使用 verbose 執行`--verbose`(Linux) 和`/V`(Windows) 選項中，並且輸出已重新導向到記錄檔。 

在本教學課程， [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx)用來在 Windows 中，建立排定的工作和[Crontab](http://crontab.org/)命令可用來建立 Linux 上的 cron 作業。 
 **Schtasks**讓系統管理員可以建立、 刪除、 查詢、 變更、 執行和結束本機或遠端電腦上的排程的工作。 **Cron**可讓 Linux 和 Unix 使用者執行命令或指令碼在指定的日期和時間使用[cron 運算式](https://en.wikipedia.org/wiki/Cron#CRON_expression)


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
**若要建立 Linux 上的 cron 作業**，輸入下列命令，在終端機上。 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

指定 cron 運算式`*/5 * * * * `命令中指出的殼層指令碼`script.sh`應該執行每隔五分鐘。 指令碼可以排程在特定時間執行每日、 每月或每年。 請參閱[cron 運算式](https://en.wikipedia.org/wiki/Cron#CRON_expression)若要深入了解設定日期和時間執行的作業。 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
**若要在 Windows 上建立排定的工作**，PowerShell 命令提示字元上輸入下列命令。

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

此命令會使用`/SC`參數來指定分鐘數的排程和`/MO`參數來指定一段五分鐘。 `/TN`參數用來指定工作名稱，和`/TR`參數來指定的路徑`script.bat`檔案。 請瀏覽[schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes)若要深入了解在 Windows 上建立排定的工作。

---
 
若要驗證已排程的工作/cron 作業會正確執行，您目錄中建立新檔案`myfolder`。 稍候五分鐘來確認新的檔案已上傳至儲存體帳戶。 移至您的記錄目錄，以檢視已排程的工作 cron 作業的輸出記錄檔。 

請瀏覽[移動在內部部署資料](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)深入了解移動到 Azure 儲存體，反之亦然，在內部部署資料的方式。  

## <a name="next-steps"></a>後續步驟
如需關於 Azure 儲存體和 AzCopy 的詳細資訊，請參閱下列資源：

### <a name="azure-storage-documentation"></a>Azure 儲存體文件：
* [Azure 儲存體簡介](../storage-introduction.md)
* [利用 azcopy 進行 Windows 上的傳送資料](storage-use-azcopy.md) 
* [使用 AzCopy on Linux 傳送資料](storage-use-azcopy-linux.md) 
* [建立儲存體帳戶](../storage-create-storage-account.md)
* [使用儲存體總管來管理 Blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) \(英文\)  






 
 

