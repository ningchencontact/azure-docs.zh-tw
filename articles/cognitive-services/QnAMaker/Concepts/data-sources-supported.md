---
title: 支援的資料來源 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 支援的資料來源
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369655"
---
# <a name="data-sources"></a>資料來源 
QnA Maker 可自動從常用的半結構化內容格式中擷取問答組，例如常見問題集和產品手冊。 內容也可以從結構化檔案新增至知識庫。

## <a name="plain-faq-pages"></a>一般的常見問題集頁面
這是最常用的常見問題集頁面類型，在此處，解答會在相同的頁面中緊接在問題後面。 

![一般的常見問題集頁面](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>具有區段連結的常見問題集頁面 
在這種類型的常見問題集頁面中，問題彙總在一起，以及連結至相同頁面上位於不同區段中的解答。

 ![區段連結常見問題集頁面](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>有連結連至不同頁面的常見問題集頁面 
這種類型的常見問題集頁面與區段連結的常見問題集頁面相似，不同之處在於其連結會重新導向至其他頁面。 QnA Maker 會搜耙所有連結頁面，以擷取對應的解答。

 ![深層連結常見問題集頁面](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>產品手冊

手冊通常式隨附於產品的指引內容。 它可協助使用者設定、使用、維護產品，並對其問題進行疑難排解。 當 QnA Maker 處理手冊時，它會將標題和子標題擷取為問題，並將後續的內容擷取為解答。 請參閱[這裡](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的範例。

> [!NOTE]
> 手冊若具有目錄及/或索引頁面，和包含階層式標題的明確結構，擷取將會有最佳效果。


## <a name="structured-data-format-through-file-upload"></a>透過檔案上傳的結構化資料格式

具有格式化資料行的結構化檔案 (例如 .xlsx、tsv) 也可在知識庫建立期間上傳至 QnA Maker。 您也可以從知識庫的 [設定] 索引標籤上傳檔案

| 問題  | Answer  | 中繼資料                |
|-----------|---------|-------------------------|
| 問題 1 | 解答 1 | `Key1:Value1\|Key2:Value2` |
| 問題 2 | 解答 2 |      `Key:Value`           |
來源檔案中的任何其他資料行都會被忽略。

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式
匯入知識庫後，將會取代現有知識庫的內容。 匯入時必須要有包含資料來源資訊的結構化 .tsv 檔案。 這項資訊可協助 QnA Maker 將問答組分組，並將其歸於特定資料來源。

| 問題  | Answer  | 來源| 中繼資料                |
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1|`Key1:Value1\|Key2:Value2` |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

## <a name="editorial"></a>編輯
如果您沒有既有的內容可填入知識庫，您也可以透過編輯將內容新增至 QnA Maker 知識庫中。 在[這裡](../How-To/edit-knowledge-base.md)了解如何更新知識庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定 QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>另請參閱 

[QnA Maker 概觀](../Overview/overview.md)