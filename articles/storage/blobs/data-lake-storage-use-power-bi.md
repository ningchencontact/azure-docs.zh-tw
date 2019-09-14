---
title: 使用 Power BI 分析 Azure Data Lake Storage Gen2 中的資料 |Microsoft Docs
description: 使用 Power BI 來分析儲存在 Azure Data Lake Storage Gen2 中的資料
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: d76ea317271ae0e8eb0d54fcfee5dc005d836fc1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984950"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>使用 Power BI 分析 Azure Data Lake Storage Gen2 中的資料

在本文中，您將瞭解如何使用 Power BI Desktop 來分析儲存在具有階層命名空間（Azure Data Lake Storage Gen2）之儲存體帳戶中的資料並加以視覺化。

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 具有階層命名空間的儲存體帳戶。 請遵循[這些](data-lake-storage-quickstart-create-account.md)指示來建立一個。
> 本文假設您已建立名為`myadlsg2`的帳戶。
> * 名為`Drivers.txt`的範例資料檔案位於您的儲存體帳戶中。
> 您可以從[Azure Data Lake Git 存放庫](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)下載此範例，然後將該檔案上傳至您的儲存體帳戶。
> * **Power BI Desktop**。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=45331)下載此項目。 

## <a name="create-a-report-in-power-bi-desktop"></a>在 Power BI Desktop 中建立報表

1. 在您的電腦上啟動 Power BI Desktop。
2. 從功能區的 [**首頁**] 索引標籤中，按一下 [**取得資料**]，然後按一下 [**其他**]。
3. 在 [**取得資料**] 對話方塊中，按一下 [ **Azure**]，按一下 [ **Azure Data Lake 存放區 Gen2 （Beta）** ]，然後按一下 **[連接]** 。

    ![[取得資料] 頁面](media/data-lake-storage-use-power-bi/get-data-page.png)

4. 在 [ **Azure Data Lake Storage Gen2** ] 對話方塊中，您可以使用容器端點格式，提供您 Azure Data Lake Storage Gen2 帳戶、filesystem 或子資料夾的 URL。 Data Lake Storage Gen2 的 url 具有下列模式`https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` ，然後按一下 **[確定]** 。

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. 在下一個對話方塊中，按一下 [登**入**] 以登入您的儲存體帳戶。 系統會將您重新導向至組織的登入頁面。 遵循提示登入此帳戶。

    ![登入頁面](media/data-lake-storage-use-power-bi/sign-in.png)

6. 成功登入之後，請按一下 **[連線]** 。

    ![已登入頁面](media/data-lake-storage-use-power-bi/signed-in.png)

7. 下一個對話方塊會顯示您在上述步驟4中提供的 URL 底下的所有檔案，包括您上傳至儲存體帳戶的檔案。 確認資訊，然後按一下 [**載入**]。

    ![檔案系統](media/data-lake-storage-use-power-bi/file-systems.png)

8. 成功將資料載入 Power BI 之後，您會在 [**欄位**] 索引標籤中看到下欄欄位。

    ![欄位索引標籤](media/data-lake-storage-use-power-bi/fields.png)

    不過，若要將資料視覺化並加以分析，我們偏好根據下欄欄位提供資料。

    ![欄位](media/data-lake-storage-use-power-bi/preferred-fields.png)

    在後續步驟中，我們將更新查詢，以轉換所需格式的已匯入資料。

9. 從功能區的 [**首頁**] 索引標籤中，按一下 [**編輯查詢**]。

    ![查詢](media/data-lake-storage-use-power-bi/queries.png)

10. 在 [**查詢編輯器**] 的 [**內容**] 資料行下，按一下 [**二進位**]。 檔案將會自動偵測為 CSV，您應該會看到如下所示的輸出。 您的資料現在為可用來建立視覺效果的格式。

    ![Output](media/data-lake-storage-use-power-bi/binary.png)

11. 從功能區的 [**首頁**] 索引標籤中，按一下 [**關閉**並套用]，然後按一下 [**關閉** **並套用** **]。**

    ![關閉並套用](media/data-lake-storage-use-power-bi/close-apply.png)

12. 更新查詢之後，[欄位] 索引標籤將會顯示可用於視覺效果的新欄位。

    ![新欄位](media/data-lake-storage-use-power-bi/new-fields.png)

13. 讓我們建立圓形圖，以代表指定國家/地區的每個城市中的驅動程式。 若要這麼做，請進行下列選擇。

    從 [**視覺效果**] 索引標籤中，按一下圓形圖的符號。

    ![視覺效果](media/data-lake-storage-use-power-bi/visualizations.png)

    我們即將使用的資料行是 [資料行 4] （城市名稱）和 [資料行 7] （國家/地區名稱）。 將這些資料行從 [欄位] 索引標籤拖曳到 [視覺效果] 索引標籤 (如下所示)。

    ![拖曳欄位](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    圓形圖現在應該與下面類似。

    ![圓形圖](media/data-lake-storage-use-power-bi/pie-chart.png)

14. 透過從頁面層級篩選中選取特定國家/地區，您現在可以看到所選國家/地區的每個城市中的驅動程式數目。 例如，在 [視覺效果] 索引標籤的 [頁面層級篩選] 下，選取 [巴西]。

    ![頁面篩選](media/data-lake-storage-use-power-bi/page-filters.png)

15. 圓形圖會自動更新以顯示巴西城市中的驅動程式。

    ![巴西](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. 從 [檔案] 功能表中，按一下 [儲存] 將視覺效果儲存為 Power BI Desktop 檔案。

## <a name="publish-report-to-power-bi-service"></a>將報表發佈到 Power BI 服務

在 Power BI Desktop 中建立視覺效果之後，您可以將其發佈至 Power BI 服務以與其他人共用。 如需如何執行的指示，請參閱[從 Power BI Desktop 發佈](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)。
