---
title: 從透過 HTTP 的 Web URL 匯入：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用透過 HTTP 模組在 Azure Machine Learning 服務中的從 Web URL 匯入，讀取公開的網頁，以用於機器學習實驗中的資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f0847e9dd90267d985b75be3c3a07ce8fae98a9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029666"
---
# <a name="import-from-web-url-via-http-module"></a>透過 HTTP 模組，從 Web URL 匯入

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來讀取公開的網頁，以用於機器學習實驗中的資料。

下列限制適用於在網頁上發佈的資料：

- 資料必須是其中一種支援的格式：CSV、 TSV、 ARFF 或 SvmLight。 其他資料將會造成錯誤。
- 需要或支援任何驗證。 資料必須可公開使用。 

若要取得資料的兩種方式： 使用精靈來設定資料來源，或手動設定它。

## <a name="use-the-data-import-wizard"></a>使用資料匯入精靈

1. 新增**匯入資料**模組至您的實驗。 您可以尋找在介面中，模組中**資料輸入和輸出**類別目錄。

2. 按一下 **啟動匯入資料精靈**，然後選取 透過 HTTP 的 Web URL。

3. 在 URL 中，貼上，選取的資料格式。

4. 當已完成設定。

若要編輯現有的資料連接，請重新啟動精靈。 精靈會載入所有先前的組態詳細資料，讓您不必從頭重新啟動

## <a name="manually-set-properties-in-the-import-data-module"></a>手動匯入資料模組中設定屬性

下列步驟說明如何手動設定，匯入來源。

1. 新增[匯入資料](import-data.md)模組至您的實驗。 您可以尋找在介面中，模組中**資料輸入和輸出**類別目錄。

2. 針對**資料來源**，選取**透過 HTTP 的 Web URL**。

3. 針對**URL**中，輸入或貼上頁面包含您想要載入之資料的完整 URL。

    此 URL 應該包含網站 URL 和完整路徑，使用檔名和副檔名，包含要載入的資料頁面。

    比方說，下列的頁面會包含從機器學習儲存機制的 University of California，Irvine 鳶尾花資料集：

    `http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. 針對**的資料格式**，選取其中一個支援的資料格式的清單。

    我們建議您一律檢查資料事先決定格式。 UC Irvine 頁面會使用 CSV 格式。 其他支援的資料格式是 TSV、 ARFF 和 SvmLight。

5. 如果資料是 CSV 或 TSV 格式，使用**檔案具有標頭資料列**選項以指出來源資料是否包含標頭資料列。 標頭資料列用來指派資料行名稱。

6. 選取 **使用快取的結果**選項，如果您不希望資料變更，或如果您想要避免重新載入的資料每次執行實驗。

    選取此選項時，實驗會載入資料第一次模組執行時，之後使用的資料集的快取的版本。

    如果您想要重新載入實驗資料集的每個反覆項目上的資料集，請取消選取**使用快取的結果**選項。 如果有任何變更的參數也重新載入結果[匯入資料](import-data.md)。

7. 執行實驗。

## <a name="results"></a>結果

完成時，按一下 輸出資料集，然後選取**視覺化**以查看資料已成功匯入。


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 