---
title: 使用 Azure Data Factory 從多個容器複製檔案 | Microsoft Docs
description: 了解如何使用解決方案範本，透過 Azure Data Factory 從多個容器複製檔案。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966407"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>使用 Azure Data Factory 從多個容器複製檔案

本文中所述的解決方案範本可協助您從多個檔案、容器或貯體等檔案存放區之間複製檔案。 例如，您可能想將您的資料湖從 AWS S3 移轉到 Azure Data Lake Store。 或者，您想要將某個 Azure Blob 儲存體帳戶的所有內容複寫到另一個 Azure Blob 儲存體帳戶。 此範本專為這些使用案例而設計。

如果您想要從單一容器或貯體複製檔案，使用**資料複製工具**建立具有單一複製活動的管線會更有效率。 針對這類簡單使用案例，您無需使用這個範本。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會列舉您的來源儲存體存放區容器，然後將來源儲存體存放區的每個容器複製到目的地存放區。 

範本包含三個活動：
-   **GetMetadata** 活動會掃描您的來源儲存體存放區，並取得容器清單。
-   **ForEach** 活動會取得 **GetMetadata** 活動的容器清單，然後逐一查看清單並將每個容器傳遞給複製活動。
-   **複製**活動會將來源儲存體存放區的每個容器複製到目的地存放區。

範本定義兩個參數：
-   參數 *SourceFilePath* 是您的資料來源存放區路徑，您可在其中取得容器或貯體的清單。 在大部分情況下，此路徑是根目錄，其包含多個容器資料夾。 此參數的預設值為 `/`。
-   參數 *DestinationFilePath* 是檔案將複製到目的地存放區的路徑。 此參數的預設值為 `/`。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至範本**在檔案存放區之間複製多個檔案容器**，並建立與來源儲存體存放區的**新連線**。 來源儲存體存放區是您要從多個容器或貯體複製檔案的位置。

    ![建立與來源的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 建立與目的地儲存體存放區的**新連線**。

    ![建立與目的地的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 按一下 [使用此範本]。

    ![使用此範本](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 您會在面板中看見可用的管線，如下列範例所示：

    ![顯示管線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 按一下 [偵錯]、輸入**參數**，然後按一下 [完成]。

    ![執行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
