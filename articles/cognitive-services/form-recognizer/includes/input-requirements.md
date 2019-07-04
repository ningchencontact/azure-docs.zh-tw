---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 690219347782aad2140b0a1b0541590c5426c568
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459736"
---
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