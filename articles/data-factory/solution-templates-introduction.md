---
title: Azure Data Factory 的範本概觀 | Microsoft Docs
description: 了解如何使用預先定義的範本來快速開始使用 Azure Data Factory。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60786909"
---
# <a name="templates"></a>範本

範本為預先定義的 Azure Data Factory 管線，可讓您迅速開始使用 Data Factory。 範本很適合在您不熟悉 Data Factory 且想要快速開始使用的情況下使用。 這些範本會減少建置資料整合專案的開發時間，並進一步改善開發人員生產力。

## <a name="create-data-factory-pipelines-from-templates"></a>從範本建立 Data Factory 管線

您可以透過下列兩種方法從範本開始建立 Data Factory 管線：

1.  選取 [概觀] 頁面上的 [從範本建立管線]  來開啟 [範本資源庫]。

    ![從 [概觀] 頁面開啟 [範本資源庫]](media/solution-templates-introduction/templates-intro-image1.png)

1.  在資源總管中的 [撰寫] 索引標籤上，選取 **+** ，然後選取 [來自範本的管線]  以開啟 [範本資源庫]。

    ![從 [撰寫] 索引標籤開啟 [範本資源庫]](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>範本資源庫

![範本資源庫](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>立即可用的 Data Factory 範本

Data Factory 使用 Azure Resource Manager 範本來儲存 Data Factory 管線範本。 您可以看到所有 Resource Manager 範本，以及用來利用方塊 Data Factory 範本中中的資訊清單檔案[官方 Azure 資料處理站 GitHub 存放庫](https://github.com/Azure/Azure-DataFactory/tree/master/templates)。 由 Microsoft 所提供的預先定義範本包含 (但不限於) 下列項目：

-   複製範本：

    -   [從資料庫進行大量複製](solution-template-bulk-copy-with-control-table.md)
    
    -   [將新的檔案，由 LastModifiedDate 複製](solution-template-copy-new-files-lastmodifieddate.md)

    -   [在檔案型存放區之間複製多個檔案容器](solution-template-copy-files-multiple-containers.md)

    -   [從資料庫進行差異複製](solution-template-delta-copy-with-control-table.md)

    -   從 \<來源\> 複製到 \<目的地\>

        -   從 Amazon S3 複製到 Azure Data Lake Store Gen 2

        -   從 Google Big Query 複製到 Azure Data Lake Store Gen 2

        -   從 HDF 複製到 Azure Data Lake Store Gen 2

        -   從 Netezza 複製到 Azure Data Lake Store Gen 1

        -   從 SQL Server 內部部署複製到 Azure SQL Database

        -   從 SQL Server 內部部署複製到 Azure SQL 資料倉儲

        -   從 Oracle 內部部署複製到 Azure SQL 資料倉儲

-   SSIS 範本

    -   對 Azure-SSIS Integration Runtime 進行排程以執行 SSIS 套件

-   轉換範本

    -   [搭配 Azure Databricks 的 ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>我的範本

您也可以選取 [管線] 索引標籤上的 [儲存為範本]  來將管線儲存為範本。

![選取管線作為範本](media/solution-templates-introduction/templates-intro-image4.png)

您可以在 [範本資源庫] 的 [我的範本]  區段檢視儲存為範本的管線。 您也可以在資源總管中的 [範本]  區段中看見它們。

![我的範本](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> 若要使用 [我的範本] 功能，您必須啟用 GIT 整合。 同時支援 Azure DevOps GIT 和 GitHub。
