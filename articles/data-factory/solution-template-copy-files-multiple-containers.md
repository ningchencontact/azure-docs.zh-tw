---
title: 複製多個容器中的檔案，藉由使用 Azure Data Factory |Microsoft Docs
description: 了解如何從多個容器複製檔案，藉由使用 Azure Data Factory 中使用的解決方案範本。
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
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635136"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>使用 Azure Data Factory 從多個容器複製檔案

此文章說明您可用來從多個容器檔案存放區之間複製檔案的解決方案範本。 比方說，您可以使用它來將您的 data lake 從 AWS S3 移轉到 Azure Data Lake Store。 或者，您可以使用範本來將所有項目從一個 Azure Blob 儲存體帳戶複寫到另一個。

> [!NOTE]
> 如果您想要在單一容器中的檔案複製，則使用更有效率[複製資料工具](copy-data-tool.md)來建立具有單一複製活動的管線。 這篇文章中的範本可以是多個您需要針對該簡單的案例。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會列舉您來源儲存體儲存的容器。 它接著會將這些容器複製到目的地存放區。

範本包含三個活動：
- **GetMetadata**會掃描您的來源儲存體存放區，並取得容器清單。
- **ForEach**取得的容器清單**GetMetadata**活動然後逐一查看清單，並傳遞給複製活動的每個容器。
- **複製**將每個容器從來源儲存體存放區複製到目的地存放區。

範本定義兩個參數：
- *SourceFilePath*是您的資料來源存放區，您可以在其中取得的容器清單的路徑。 在大部分情況下，此路徑是根目錄，其包含多個容器資料夾。 此參數的預設值為 `/`。
- *目標檔案路徑*是其中的檔案將會複製到目的地存放區中的路徑。 此參數的預設值為 `/`。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至**複製檔案存放區之間的多個檔案容器**範本。 建立**新增**連接到您來源儲存體存放區。 來源儲存體存放區是您要從多個容器中的檔案複製。

    ![建立與來源的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 建立**新增**連接至您的目的地儲存體存放區。

    ![建立與目的地的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 選取 **使用此範本**。

    ![使用此範本](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 您會看到管線中的，如下列範例所示：

    ![顯示管線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 選取 **偵錯**，輸入**參數**，然後選取**完成**。

    ![執行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 中的一個控制資料表的資料庫中的大量複製](solution-template-bulk-copy-with-control-table.md)

- [使用 Azure Data Factory 的多個容器中的檔案複製](solution-template-copy-files-multiple-containers.md)