---
title: 什麼是表單辨識器？
titleSuffix: Azure Cognitive Services
description: 了解如何使用表單辨識器來剖析表單和資料表資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601618"
---
# <a name="what-is-form-recognizer"></a>什麼是表單辨識器？

Azure 表單辨識器是一項認知服務，其使用機器學習技術來識別和擷取表單文件中的索引鍵值組和資料表資料。 然後，它會輸出在原始檔中包含關聯性的結構化資料。 您可以使用簡單的 REST API 呼叫您的自訂表單辨識器模型，以降低複雜度並輕鬆地將其整合至工作流程或應用程式。 若要開始，您只需要以五個表單文件或相同類型的空白表單作為輸入資料。 您可以快速取得針對特定內容量身打造的精確結果，而不需要大量手動操作或廣泛的資料科學專業知識。

## <a name="request-access"></a>要求存取
表單辨識器是以有限存取預覽版的形式提供。 若要存取此預覽服務，請先填寫並提交[表單辨識器存取要求](https://aka.ms/FormRecognizerRequestAccess)表單。 該表單會要求您提供您個人、您的公司，以及您會在什麼樣的使用者案例下使用表單辨識器的相關資訊。 如果 Azure 認知服務小組核准要求，您就會收到一封電子郵件，裡面會指示您如何存取該服務。

## <a name="what-it-does"></a>作用

當您提交輸入資料時，演算法會據此定型、依類型將表單群集、探索目前有哪些索引鍵和資料表，並學習將值與索引鍵以及將項目與資料表相關聯。 非監督式學習可讓模型了解欄位與項目之間的配置和關聯性，而不需要手動資料標記或密集編碼和維護。 相較之下，預先定型的機器學習模型則需要標準化的資料，而且在使用衍生自傳統格式 (例如，業界特有表單) 的輸入資料時，精確性會比較差。

定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

## <a name="what-it-includes"></a>包含內容

表單辨識器是以 REST API 的形式提供。 您可以叫用 API 來建立、定型和評分模型。 如果您想的話，也可以在本機 Docker 容器中執行模型。

## <a name="input-requirements"></a>輸入需求

表單辨識器可處理符合下列需求的輸入文件：

* 格式必須為 JPG、PNG 或 PDF (文字或掃描)。 內嵌文字的 PDF 更好，因為在擷取和定位字元時完全不可能發生錯誤。
* 檔案大小必須小於 4 MB。
* 針對影像，維度必須介於 50 x 50 像素和 4200 x 4200 像素之間。
* 如果掃描自書面文件，表單應該採用高品質的掃描方式。
* 文字必須使用拉丁字母 (英文字元)。
* 必須是列印資料 (不是手寫)。
* 資料必須包含索引鍵和值。
* 索引鍵可出現在值的上方或左邊，而不能在下方或右邊。

表單辨識器目前不支援這些類型的輸入資料：

* 複雜資料表 (巢狀資料表、合併的標題或資料格等等)。
* 核取方塊或選項按鈕。
* 超過 50 頁的 PDF 文件。

## <a name="where-do-i-start"></a>我該從哪裡開始？

**步驟 1：** 在 Azure 入口網站中建立表單辨識器資源。

**步驟 2：** 嘗試快速入門以獲得實際操作經驗：
* [快速入門：搭配使用 REST API 與 cURL 來將表單辨識器模型定型並擷取表單資料](quickstarts/curl-train-extract.md)
* [快速入門：搭配使用 REST API 與 Python 來將表單辨識器模型定型並擷取表單資料](quickstarts/python-train-extract.md)

當您正在學習技術時，我們建議您使用免費服務，但請記住，可用頁數僅限於每個月 500 頁。

**步驟 3：** 檢閱 REST API

使用下列 API 來定型和擷取表單中的結構化資料。

| REST API | 說明 |
|-----|-------------|
| 定型 | 使用相同類型的 5 個表單或 1 個空白表單來將新模型定型，以分析您的表單。  |
| 分析  |分析以資料流形式傳入的一份文件，以使用自訂模型從表單擷取索引鍵/值組和資料表。  |

探索 [REST API 參考文件](https://aka.ms/form-recognizer/api)。 

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

此服務是依據[線上服務條款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)以 Azure 服務的[預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)形式來提供。 和所有認知服務一樣，使用表單辨識器服務的開發人員應該要了解 Microsoft 對於客戶資料的政策。 請參閱 Microsoft 信任中心上的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)，以進行深入了解。

## <a name="next-steps"></a>後續步驟

請完成[快速入門](quickstarts/curl-train-extract.md)來開始使用[表單辨識器 API](https://aka.ms/form-recognizer/api)。
