---
title: 將資料從 Azure 資料總管使用 Sisense 視覺化
description: 在這篇文章，了解如何設定 Azure 資料總管做為資料來源之 Sisense，並將資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358179"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>將資料從 Azure 中的資料總管 Sisense 視覺化

Sisense 是分析商業智慧平台，可讓您建置分析應用程式可提供高度互動的使用者體驗。 商業智慧與報告軟體的儀表板可讓您存取，並在按幾下中合併資料。 您可以連接到結構化和非結構化資料來源、 聯結多個來源的最小的指令碼和撰寫程式碼，從資料表和建立互動式 web 儀表板和報表。 在本文中，您將了解如何設定 Azure 資料總管做為資料來源之 Sisense，並將從範例的叢集中的資料視覺化。

## <a name="prerequisites"></a>必要條件

您需要下列項目完成這篇文章：

* [下載並安裝 Sisense 應用程式](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* 建立叢集和資料庫，其中包含 StormEvents 範例資料。 如需詳細資訊，請參閱[快速入門：建立 Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)及[將範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>連接到使用 Azure 資料總管 JDBC 連接器 Sisense 儀表板

1. 下載並複製下列 jar 檔案，以最新版本 *...\Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. 開啟**Sisense**應用程式。
1. 選取 **資料**索引標籤，然後選取 **+ ElastiCube**來建立新的 ElastiCube 模型。
    
    ![選取 ElastiCube](media/sisense/data-select-elasticube.png)

1. 在 **加入新的 ElastiCube 模型**，ElastiCube 模型並**儲存**。
   
    ![加入新的 ElastiCube 模型](media/sisense/add-new-elasticube-model.png)

1. 選取  **+ 資料**。

    ![選取 [資料] 按鈕](media/sisense/select-data.png)

1. 在 **選取 連接器**索引標籤上，選取**一般 JDBC**連接器。

    ![選擇 JDBC 連接器](media/sisense/select-connector.png)

1. 在 [ **Connect**索引標籤上，完成下列欄位進行**一般 JDBC**連接器，然後選取**下一步]** 。

    ![JDBC 連接器設定](media/sisense/jdbc-connector.png)

    |欄位 |描述 |
    |---------|---------|
    |連接字串     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC Jar 的資料夾  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |驅動程式的類別名稱    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |使用者名稱   |    AAD 的使用者名稱     |
    |密碼     |   AAD 使用者密碼      |

1. 在 **選取資料**索引標籤，搜尋**選取資料庫**，選取您具有權限的相關資料庫。 在此範例中，選取*test1*。

    ![選取資料庫](media/sisense/select-database.png)

1. 在 [*測試*（資料庫名稱）] 窗格：
    1. 選取要預覽資料表，並查看資料表資料行名稱的資料表名稱。 您可以移除不必要的資料行。
    1. 選取核取方塊以選取該資料表的相關資料表。 
    1. 選取 [完成]  。

    ![選取資料表](media/sisense/select-table-see-columns.png)    

1. 選取 **建置**來建置您的資料集。 

    * 在 **建置**視窗中，選取**建置**。

      ![建置視窗](media/sisense/build-window.png)

    * 請等候建置程序完成，然後選取**建置成功**。

      ![建置成功](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>建立 Sisense 儀表板

1. 在  **Analytics**索引標籤上，選取 **+**  > **新儀表板**上此資料集建立儀表板。

    ![新增儀表板](media/sisense/new-dashboard.png)

1. 選取儀表板，然後選取**建立**。 

    ![建立儀表板](media/sisense/create-dashboard.png)

1. 下**新的 Widget**，選取**選取 資料 +** 來建立新的 widget。 

    ![將欄位加入至 StormEvents 儀表板](media/sisense/storm-dashboard-add-field.png)  

1. 選取  **+ 新增更多資料**新增至您的圖形的其他資料行。 

    ![將更多資料新增至圖表](media/sisense/add-more-data.png)

1. 選取  **+ Widget**建立另一個小工具。 拖放以重新排列儀表板的小工具。

    ![Storm 儀表板](media/sisense/final-dashboard.png)

現在可以瀏覽使用視覺化分析資料、 建立其他儀表板，並將資料轉換成可操作的見解，以對您的業務影響。

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)

