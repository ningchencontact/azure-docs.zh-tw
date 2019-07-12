---
title: 連結 Azure Data Factory 中的服務 |Microsoft Docs
description: 深入了解 Data Factory 中的連結服務。 已連結的服務會將計算/資料存放區連結至資料處理站。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870128"
---
# <a name="linked-services-in-azure-data-factory"></a>Azure Data Factory 中的連結的服務
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-create-datasets.md)
> * [目前的版本](concepts-datasets-linked-services.md)

這篇文章說明哪些連結的服務、 以 JSON 格式的定義方式以及如何使用 Azure Data Factory 管線。

如果您不熟悉 Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)來概略了解。

## <a name="overview"></a>總覽
資料處理站可以有一或多個管線。 「管線」  是一起執行某個工作的「活動」  所組成的邏輯群組。 管線中的活動會定義要在資料上執行的動作。 例如，您可以使用複製活動將資料從內部部署 SQL Server 複製到 Azure Blob 儲存體。 接著，您可以使用在 Azure HDInsight 叢集上執行 Hive 指令碼的 Hive 活動，來處理來自 Blob 儲存體的資料以產生輸出資料。 最後，您可以使用第二個複製活動將輸出資料複製到「Azure SQL 資料倉儲」，以在該處建置商業智慧 (BI) 報表解決方案。 如需有關管線和活動的詳細資訊，請參閱 Azure Data Factory 中的[管線和活動](concepts-pipelines-activities.md)。

現在，「資料集」  是一個具名的資料檢視，指向或參考您想要在「活動」  中用來作為輸入或輸出的資料。

在您建立資料集之前，您必須建立一個「已連結的服務」  ，以將資料存放區連結到資料處理站。 已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這麼說吧：資料集代表已連結之資料存放區內的資料結構，而已連結的服務則定義與資料來源的連線。 例如，「Azure 儲存體」已連結服務會將儲存體帳戶連結到 Data Factory。 Azure Blob 資料集代表該 Azure 儲存體帳戶內包含要處理之輸入 Blob 的 Blob 容器和資料夾。

以下是一個範例案例。 若要將資料從 Blob 儲存體複製到 SQL Database，您需建立兩個連結服務：類型為 Azure 儲存體和 Azure SQL Database。 接著，建立兩個資料集：Azure Blob 資料集 (此資料集參考 Azure 儲存體連結服務) 和 Azure SQL 資料表資料集 (此資料集參考 Azure SQL Database 連結服務)。 「Azure 儲存體」和 Azure SQL Database 已連結服務包含 Data Factory 在執行階段分別用來連接到「Azure 儲存體」和 Azure SQL Database 的連接字串。 Azure Blob 資料集會指定包含 Blob 儲存體中輸入 Blob 的 Blob 容器和 Blob 資料夾。 「Azure SQL 資料表」資料集會指定作為資料複製目的地的 SQL Database 中 SQL 資料表。

下圖顯示 Data Factory 中管線、活動、資料集及已連結服務之間的關聯性：

![管線、活動、資料集、已連結的服務之間的關聯性](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>連結服務 JSON
Data Factory 中的連結服務會以 JSON 格式定義如下：

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

下表描述上述 JSON 的屬性：

屬性 | 描述 | 必要項 |
-------- | ----------- | -------- |
name | 連結服務的名稱。 請參閱 [Azure Data Factory - 命名規則](naming-rules.md)。 |  是 |
type | 連結服務的類型。 例如: AzureStorage (資料存放區) 或 AzureBatch (計算)。 請參閱 typeProperties 的描述。 | 是 |
typeProperties | 每個資料存放區和計算的類型屬性都不同。 <br/><br/> 如需支援的資料存放區類型及其類型屬性，請參閱本文章的[資料集類型](concepts-datasets-linked-services.md#dataset-type)表格。 請瀏覽資料存放區連接器的文章，以了解資料存放區特有的類型屬性。 <br/><br/> 如需支援的計算類型與其類型屬性，請參閱[計算連結服務](compute-linked-services.md)。 | 是 |
connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否

## <a name="linked-service-example"></a>已連結的服務範例
以下連結服務是 Azure 儲存體連結服務。 請注意，類型已設為 AzureStorage。 Azure 儲存體連結服務的類型屬性包含連接字串。 Data Factory 服務會在執行階段使用連接字串來連線至資料存放區。

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>建立連結的服務
您可以使用其中一種工具或 Sdk 來建立連結的服務： [.NET API](quickstart-create-data-factory-dot-net.md)， [PowerShell](quickstart-create-data-factory-powershell.md)， [REST API](quickstart-create-data-factory-rest-api.md)，Azure Resource Manager 範本和 Azure 入口網站

## <a name="data-store-linked-services"></a>資料存放區連結的服務
連接到資料存放區可在我們[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。 參考所需的不同存放區的特定連接屬性的清單。

## <a name="compute-linked-services"></a>計算連結服務
參考[支援計算環境](compute-linked-services.md)詳細不同計算環境您可以從您的 data factory，以及不同的設定連線到。

## <a name="next-steps"></a>後續步驟
如需使用上述其中一個工具或 SDK 來建立管線和資料集的逐步指示，請參閱下列教學課程。

- [快速入門：使用 .NET 來建立資料處理站](quickstart-create-data-factory-dot-net.md)
- [快速入門：使用 PowerShell 來建立資料處理站](quickstart-create-data-factory-powershell.md)
- [快速入門：使用 REST API 來建立資料處理站](quickstart-create-data-factory-rest-api.md)
- [快速入門： 建立資料處理站使用 Azure 入口網站](quickstart-create-data-factory-portal.md)
