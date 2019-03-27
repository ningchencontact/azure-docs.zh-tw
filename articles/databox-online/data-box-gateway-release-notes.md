---
title: Azure Data Box 閘道一般可用性版本資訊 |Microsoft Docs
description: 描述執行正式運作版本的 Azure 資料方塊閘道的重大未決問題和解決方式。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499874"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure 資料方塊 Edge/Azure Data Box 閘道一般可用性版本資訊

## <a name="overview"></a>概觀

下列版本資訊識別的重大未決問題和已解決的問題的公開上市 (GA) 版本的 Azure 資料方塊邊緣和 Azure 中部署資料閘道。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 部署您的資料方塊邊緣/資料方塊閘道之前，請仔細檢閱版本資訊中所包含的資訊。

GA 版本對應於軟體版本：

- **資料方塊閘道 1903 (1.5.814.447)**
- **資料方塊邊緣 1903 (1.5.814.447)**


## <a name="whats-new"></a>新功能

- **新的虛擬磁碟映像**-新的 VHDX 和 VMDK 現在已可在 Azure 入口網站。 下載這些映像來佈建、 設定和部署新的資料方塊閘道 GA 裝置。 無法將在舊的預覽版本中建立的資料箱閘道裝置更新為此版本。 如需詳細資訊，請移至[準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
- **NFS 支援**-NFS 支援目前為預覽狀態，且適用於 v3.0 和 v4.1 存取資料方塊邊緣和資料方塊閘道裝置的用戶端。
- **儲存復原**-您的資料方塊邊緣裝置可以承受失敗的一個資料磁碟與儲存體恢復功能。 此功能目前為預覽狀態。 您可以啟用儲存體恢復功能選取**復原**選項**儲存體設定**本機 web UI。


## <a name="known-issues-in-ga-release"></a>GA 版本的已知的問題

下表提供的已知問題的摘要資料方塊閘道的執行版本。

| 沒有。 | 功能 | 問題 | 因應措施/註解 |
| --- | --- | --- | --- |
| **1.** |檔案類型 | 不支援下列檔案類型： 字元檔案、 區塊檔案、 通訊端、 管道、 符號連結。  |複製這些檔案會導致在 NFS 共用上建立長度為 0 的檔案。 這些檔案處於錯誤狀態，也會在 error.xml 中回報。 <br> 目錄的符號連結導致目錄永遠不會標示為離線。 因此，您可能不會在目錄上看到灰色叉叉，此圖示表示目錄已離線，而且所有相關聯的內容皆已完整上傳至 Azure。 |
| **2.** |刪除 | 由於此版本有錯誤，如果已刪除 NFS 共用，則無法刪除此共用。 共用狀態會顯示「刪除中」。  |當共用使用不支援的檔案名稱時，才會發生這種情況。 |
| **3.** |複製 | 資料複製失敗，錯誤如下：無法完成要求的作業，因為檔案系統有限制。  |不支援替代資料 Stream (ADS) 相關聯的檔案大小大於 128 KB。   |


## <a name="next-steps"></a>後續步驟

- [準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
- [準備部署 Azure 資料方塊邊緣](data-box-edge-deploy-prep.md)。
