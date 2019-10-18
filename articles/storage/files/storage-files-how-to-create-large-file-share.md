---
title: 啟用和建立大型檔案共用-Azure 檔案儲存體
description: 在本文中，您將瞭解如何啟用和建立大型檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518417"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>如何啟用和建立大型檔案共用

一開始，標準檔案共用最多隻能相應增加至5個 TiB，現在，使用大型檔案共用時，可以相應增加至 100 TiB。 Premium 檔案共用預設會相應增加至 100 TiB。 

若要使用標準檔案共用相應增加至 100 TiB，您必須啟用儲存體帳戶以使用大型檔案共用。 您可以啟用現有的帳戶，或建立新的帳戶以使用大型檔案共用。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="restrictions"></a>限制

已啟用大型檔案共用的帳戶支援 LRS 或 ZRS。 目前，已啟用大型檔案共用的帳戶不支援切換、GRS 或 RA-GRS。 在帳戶上啟用大型檔案共用是無法復原的程式，一旦啟用，您的帳戶就無法轉換成切換、GRS 或 RA-GRS。

## <a name="create-a-new-storage-account"></a>建立新的儲存體帳戶

登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中，輸入**儲存體帳戶**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [儲存體帳戶]。
1. 在出現的 [儲存體帳戶] 視窗上，選擇 [新增]。
1. 選取要在其中建立儲存體帳戶的訂用帳戶。
1. 在 [資源群組] 欄位下方，選取 [新建]。 輸入新資源群組的名稱，如下圖所示。

    ![示範如何在入口網站中建立資源群組的螢幕擷取畫面](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。
1. 選取儲存體帳戶的 [位置]，並確定它是[支援 LFS 的其中一個區域](storage-files-planning.md#regional-availability)。
1. 將複寫設定為**本機多餘的儲存體**或**區域多餘的儲存體**。
1. 讓這些欄位設定為其預設值：

   |欄位  |Value  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |效能     |Standard         |
   |帳戶類型     |StorageV2 (一般用途 v2)         |
   |存取層     |經常性存取         |

1. 選取 [ **Advanced** ]，然後針對**大型檔案共用**選取 [**已啟用**]。
1. 選取 [檢閱 + 建立]，以檢閱您的儲存體帳戶設定並建立帳戶。

    ![large-file-shares-advanced-enable .png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 選取 [建立]。

## <a name="enable-on-existing-account"></a>在現有帳戶上啟用

您也可以在現有的帳戶上啟用大型檔案共用。 如果您這樣做，帳戶將無法再轉換成切換、GRS 或 RA-GRS。 此帳戶的此選擇無法復原。

1. 開啟[Azure 入口網站](https://portal.azure.com)，然後流覽至您想要在其上啟用大型檔案共用的儲存體帳戶。
1. 開啟儲存體帳戶，**然後選取 [** 設定]。
1. 選取 [在**大型檔案共用**上**啟用**]，然後選取 [儲存]。
1. 選取 **[總覽**]，然後選取 [重新整理 **]。**

![enable-large-file-shares-on-existing .png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

您現在已在儲存體帳戶上啟用大型檔案共用。

如果您收到下列錯誤：「帳戶尚無法使用大型檔案共用」。 您可以稍候一段時間，因為您的區域可能正在完成推出，或如果您有緊急需求，請與支援人員聯繫。

## <a name="create-a-large-file-share"></a>建立大型檔案共用

建立大型檔案共用幾乎等同于建立標準檔案共用。 主要差異在於您可以將配額設定為最多 100 TiB。

1. 從您的儲存體帳戶中，選取 [檔案**共用**]。
1. 選取 [+ 檔案共用]。
1. 輸入檔案共用的名稱，以及（選擇性）您想要的配額大小（最多 100 TiB），然後選取 [**建立**]。 

![large-file-shares-create-share .png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>展開現有的檔案共用

在您的儲存體帳戶上啟用大型檔案共用之後，您可以將現有的共用擴充為較高的配額。

1. 從您的儲存體帳戶中，選取 [檔案**共用**]。
1. 以滑鼠右鍵按一下您的檔案共用，然後選取 [**配額**]。
1. 輸入您想要的新大小，然後選取 **[確定]** 。

![update-large-file-share-quota .png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>後續步驟

* [連線並掛接檔案共用 - Windows](storage-how-to-use-files-windows.md)
* [連線並掛接檔案共用 - Linux](../storage-how-to-use-files-linux.md)
* [連線並掛接檔案共用 - macOS](storage-how-to-use-files-mac.md)