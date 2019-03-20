---
title: 使用 Azure Data Factory 中的 Databricks 轉換資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 中的 Databricks Notebook 以解決方案範本轉換資料。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 884a13df23fd0cc1103b6a73fad26f2099e3bfd7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074687"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>使用 Azure Data Factory 中的 Databricks 轉換資料

在本教學課程中，您將在 Data Factory 中建立包含**查閱**、**複製**和 **Databricks Notebook** 活動的端對端管線。

-   **查閱**或 GetMetadata 活動可用來確保在觸發複製與分析作業之前，來源資料集已可供下游取用。

-   **複製活動**會將來源檔案/資料集複製到接收儲存體。 接收儲存體會在 Databricks Notebook 中掛接為 DBFS，讓 Spark 可以直接取用資料集。

-   **Databricks Notebook 活動**會觸發 Databricks Notebook 以轉換資料集，並將其新增至已處理的資料夾/SQL DW。

為了簡化此範本，此範本並不會建立排程的觸發程序。 您可以視需要加以新增。

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>必要條件

1.  建立 **Blob 儲存體帳戶**，以及要作為**接收端**的容器 `sinkdata`。 請記下**儲存體帳戶名稱**、**容器名稱**和**存取金鑰**，因為後續在此範本中將會加以參考。

2.  確定您具有 **Azure Databricks 工作區**，或建立新的工作區。

1.  **匯入用於 ETL 的 Notebook**。 請將下列轉換 Notebook 匯入至 Databricks 工作區。 (其位置不必與以下所列的相同，但請記住您選擇的路徑，以供後續使用。)在 URL 欄位中輸入下列 URL，以由此 URL 匯入 Notebook：`https://Data Factorylabstaging1.blob.core.windows.net/share/Transformations.html`。 選取 [匯入]。

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  現在，我們將以您的**儲存體連線資訊** (名稱和存取金鑰) 更新**轉換** Notebook。 在上述匯入的 Notebook 中移至**命令 5**，並在取代醒目提示的值之後，將其取代為下列程式碼片段。 請確定此帳戶是先前建立的相同儲存體帳戶，且包含 `sinkdata` 容器。

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1.  產生供 Data Factory 用來存取 Databricks 的 **Databricks 存取權杖**。 **儲存存取權杖**以供後續建立 Databricks 連結服務時使用；此權杖會顯示為 'dapi32db32cbb4w6eee18b7d87e45exxxxxx'

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>建立連結服務和資料集

1.  移至 [連線 > 連結服務 > 新增]，在 Data Factory UI 中建立新的**連結服務**

    1.  **來源** – 用來存取來源資料。 在此範例中，您可以使用包含來源檔案的公用 Blob 儲存體。

        選取 [Blob 儲存體]，並使用下方的 **SAS URI** 連線至來源儲存體 (唯讀存取)。

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **接收** – 作為資料複製的目的地。

        在接收連結服務中，選取在必要條件 1 中建立的儲存體。

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – 用來連線至 Databricks 叢集

        使用在必要條件 2.c 中產生的存取金鑰建立 Databricks 連結服務。 如果您有*互動式叢集*，可加以選取。 (此範例使用 [新增作業叢集] 選項。)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  建立**資料集**

    1.  建立 **'sourceAvailability_Dataset'** 以檢查來源資料是否可用

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **來源資料集 –** 用來複製來源資料 (使用二進位複本)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **接收資料集** – 用來複製到接收/目的地位置

        1.  連結服務 - 選取在 1.b 中建立的 'sinkBlob_LS'

        2.  檔案路徑 - 'sinkdata/staged_sink'

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>建立活動

1.  建立查閱活動「可用性旗標」以進行來源可用性檢查 (可使用查閱或 GetMetadata)。 選取在 2.a 中建立的 'sourceAvailability_Dataset'。

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  建立複製活動「檔案至 Blob」，以將資料集從來源複製到接收端。 在此案例中，資料是二進位檔案。 請參考下列螢幕擷取畫面，了解複製活動中的來源和接收端組態。

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  定義**管線參數**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  建立 **Databricks 活動**

    選取在先前的步驟中建立的連結服務。

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    配置**設定**。 依照螢幕擷取畫面的說明建立**基底參數**，並建立要從 Data Factory 傳至 Databricks Notebook 的參數。 瀏覽並**選取**在**必要條件 2** 中上傳的**正確 Notebook 路徑**。

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **執行管線**。 您可以找到 Databricks 記錄的連結以取得更詳細的 Spark 記錄。

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    您也可以使用儲存體總管來確認資料檔案。 (為了建立與 Data Factory 管線執行的相互關聯，此範例會將管線執行識別碼從資料處理站附加至輸出資料夾。 如此，您即可往回追蹤每次執行所產生的檔案。)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
