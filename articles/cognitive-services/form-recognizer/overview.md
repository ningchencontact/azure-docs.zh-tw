---
title: 什麼是表單辨識器？
titleSuffix: Azure Cognitive Services
description: 了解如何使用表單辨識器來剖析表單和資料表資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: e064faf3017b95cb3a5f3d9b89f178fb7f846766
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592606"
---
# <a name="what-is-form-recognizer"></a>什麼是表單辨識器？

Azure 表單辨識器是一項認知服務，其使用機器學習技術來識別和擷取表單文件中的索引鍵/值組和資料表資料。 然後，它會輸出在原始檔中包含關聯性的結構化資料。 您可以使用簡單的 REST API 呼叫您的自訂表單辨識器模型，以降低複雜度並輕鬆地將其整合至工作流程或應用程式。 若要開始，您只需要五份已填寫的表單文件，或兩份已填寫的表單及一份相同類型的空白表單作為輸入資料。 您可以快速取得針對特定內容量身打造的精確結果，而不需要大量手動操作或廣泛的資料科學專業知識。

## <a name="custom-models"></a>自訂模型

表單辨識器自訂模型可根據您自己的資料來定型，而您只需要五個輸入表單範例即可開始。 當您提交輸入資料時，演算法會依類型將表單群集、探索目前有哪些索引鍵和資料表，並將值與索引鍵以及將項目與資料表相關聯。 然後，它會輸出在原始檔中包含關聯性的結構化資料。 定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

非監督式學習可讓模型了解欄位與項目之間的配置和關聯性，而不需要手動資料標記或密集編碼和維護。 相較之下，預先定型的機器學習模型需要標準化的資料。 這些資料較不精確，因為輸入的素材衍生自傳統格式 (例如產業特有的表單)。

## <a name="pre-built-receipt-model"></a>預先建置的收據模型

表單辨識器也包含用來讀取銷售收據的模型。 此模型會擷取索引鍵資訊，例如交易的時間和日期、商家資訊、稅金和總計的數量等等。 此外，預先建置的收據模型已定型為用來辨識及傳回收據上所有文字。

## <a name="what-it-includes"></a>包含內容

表單辨識器是以 REST API 的形式提供。 您可以藉由叫用這些 API 來建立、定型和評分自訂模型，或是存取預先建置的模型。 您也可以在本機 Docker 容器中定型並執行自訂模型。

## <a name="input-requirements-custom-model"></a>輸入需求 (自訂模型)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>要求存取

表單辨識器是以有限存取預覽版的形式提供。 若要存取此預覽服務，請先填寫並提交[表單辨識器存取要求](https://aka.ms/FormRecognizerRequestAccess)表單。 該表單會要求您提供您個人、您的公司，以及您會在什麼樣的使用者案例下使用表單辨識器的相關資訊。 如果 Azure 認知服務小組核准要求，您就會收到一封電子郵件，裡面會指示您如何存取該服務。

## <a name="where-do-i-start"></a>我該從哪裡開始？

**步驟 1：** 在 Azure 入口網站中建立表單辨識器資源。

**步驟 2：** 遵循快速入門來使用 REST API：
* [快速入門：搭配使用 REST API 與 cURL 來將表單辨識器模型定型並擷取表單資料](quickstarts/curl-train-extract.md)
* [快速入門：搭配使用 REST API 與 Python 來將表單辨識器模型定型並擷取表單資料](quickstarts/python-train-extract.md)
* [快速入門：使用 cURL 擷取收據資料](quickstarts/curl-receipts.md)
* [快速入門：使用 Python 擷取收據資料](quickstarts/python-receipts.md)

當您學習技術時，我們建議您使用免費的服務。 請記住，免費的頁數限制為每個月 500 頁。

**步驟 3：** 檢閱 REST API

您可以使用下列 API 來定型和擷取表單中的結構化資料。

|||
|---|---|
| 定型模型| 使用相同類型的 5 個表單來定型新模型，以分析您的表單。 或者，以空白表單和兩個已填寫的表單來定型模型。  |
| 分析表單 |分析以資料流形式傳入的一份文件，以使用自訂模型從表單擷取索引鍵/值組和資料表。  |
| 分析收據 |分析單一收據文件，以擷取索引鍵資訊和其他收據文字。|

若要了解更多，請瀏覽 [REST API 參考文件](https://aka.ms/form-recognizer/api)。 

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

此服務是依據[線上服務條款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)以 Azure 服務的[預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)形式來提供。 和所有認知服務一樣，使用表單辨識器服務的開發人員應該要了解 Microsoft 對於客戶資料的政策。 請參閱 Microsoft 信任中心上的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)，以進行深入了解。

## <a name="next-steps"></a>後續步驟

請完成[快速入門](quickstarts/curl-train-extract.md)來開始使用[表單辨識器 API](https://aka.ms/form-recognizer/api)。
