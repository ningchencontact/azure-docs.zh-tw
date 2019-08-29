---
title: 透過 HTTP 從 Web URL 匯入:模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的 [從 Web URL 匯入], 從公用網頁讀取資料以用於機器學習實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128745"
---
# <a name="import-from-web-url-via-http-module"></a>透過 HTTP 模組從 Web URL 匯入

本文描述適用于 Azure Machine Learning 服務的視覺化介面 (預覽) 模組。

使用此模組從公用網頁讀取資料, 以在機器學習實驗中使用。

下列限制適用于網頁上發佈的資料:

- 資料必須是其中一種支援的格式:CSV、TSV、ARFF 或 SvmLight。 其他資料會導致錯誤。
- 不需要或不支援驗證。 資料必須可公開使用。 

有兩種方式可以取得資料: 使用 wizard 來設定資料來源, 或手動進行設定。

## <a name="use-the-data-import-wizard"></a>使用資料匯入嚮導

1. 將匯**入資料**模組新增至您的實驗。 您可以在介面中的 [**資料輸入] 和 [輸出**] 分類中找到模組。

2. 按一下 [**啟動匯入資料嚮導]** , 然後選取 [透過 HTTP 的 Web URL]。

3. 貼上 URL, 然後選取資料格式。

4. 設定完成時。

若要編輯現有的資料連線, 請重新啟動精靈。 此嚮導會載入所有先前的設定詳細資料, 讓您不需要從頭開始

## <a name="manually-set-properties-in-the-import-data-module"></a>手動設定匯入資料模組中的屬性

下列步驟說明如何手動設定匯入來源。

1. 將匯[入資料](import-data.md)模組新增至您的實驗。 您可以在介面中的 [**資料輸入] 和 [輸出**] 分類中找到模組。

2. 針對 [**資料來源**], 選取 [透過**HTTP 的 Web URL**]。

3. 針對 [ **URL**], 輸入或貼上包含您要載入之資料的頁面完整 URL。

    URL 應包含網站 URL 和完整路徑 (包含檔案名和副檔名) 至包含要載入之資料的頁面。

    例如, 下列頁面包含來自加州大學機器學習存放庫的鳶尾花資料集, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. 針對 [**資料格式**], 從清單中選取其中一種支援的資料格式。

    我們建議您事先檢查資料以判斷格式。 [UC Irvine] 頁面使用 CSV 格式。 其他支援的資料格式包括 TSV、ARFF 和 SvmLight。

5. 如果資料採用 CSV 或 TSV 格式, 請使用 [檔案**具有標頭資料列**] 選項, 指示來源資料是否包含標頭資料列。 標頭資料列是用來指派資料行名稱。

6. 如果您不預期資料會有太大的變更, 或者您想要避免在每次執行實驗時重載資料, 請選取 [使用快取的**結果**] 選項。

    選取此選項時, 實驗會在第一次執行模組時載入資料, 之後會使用資料集的快取版本。

    如果您想要在實驗資料集的每個反復專案上重載資料集, 請取消選取 [使用快取的**結果**] 選項。 如果匯[入資料](import-data.md)的參數有任何變更, 也會重載結果。

7. 執行實驗。

## <a name="results"></a>結果

完成時, 按一下輸出資料集, 然後選取 [**視覺化**] 以查看資料是否已成功匯入。


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 