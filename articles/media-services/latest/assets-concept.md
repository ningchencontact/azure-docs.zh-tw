---
title: Azure 媒體服務中的資產 | Microsoft Docs
description: 本文解釋資產是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Assets

**資產** 包含數位檔案 (包括視訊、音訊、影像、縮圖集合、文字播放軌和隱藏式輔助字幕檔案)，以及這些檔案的相關中繼資料。 將數位檔案上傳到資產之後，可以用於媒體服務編碼和串流工作流程。

資產會對應到 [Azure 儲存體帳戶](storage-account-concept.md)中的 blob 容器，且資產中的檔案會儲存為該容器中的區塊 Blob。 您可以使用儲存體 SDK 用戶端，與容器中的資產檔案互動。

當帳戶使用一般用途 v2 (GPv2) 儲存體時，Azure 媒體服務支援 Blob 層。 您可以利用 GPv2，將檔案移至非經常性存取儲存體或冷儲存體。 夾層檔案不再需要時，很適合放到冷儲存體中保管 (例如，在它們編碼之後)。

在媒體服務 v3 中，可以從資產或 HTTP(s) URL 建立工作輸入。 若要建立一個資產而且可以當作工作的輸入，請參閱[從本機檔案建立工作輸入](job-input-from-local-file-how-to.md)。

此外，閱讀有關[媒體服務 中的儲存體帳戶](storage-account-concept.md)和[轉換與工作](transform-concept.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [串流處理檔案](stream-files-dotnet-quickstart.md)
