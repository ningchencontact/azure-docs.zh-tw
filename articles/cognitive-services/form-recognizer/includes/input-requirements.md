---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657744"
---
表單辨識器可處理符合下列需求的輸入文件：

* 格式必須為 JPG、PNG 或 PDF (文字或掃描)。 內嵌文字的 PDF 更好，因為在擷取和定位字元時完全不可能發生錯誤。
* 如果您的 PDF 有密碼鎖定，則必須先移除鎖定才能提交。
* 檔案大小必須小於 4 MB。
* 針對影像，維度必須介於 600 x 100 像素和 4200 x 4200 像素之間。
* 如果掃描自書面文件，表單應該採用高品質的掃描方式。
* 文字必須使用拉丁字母 (英文字元)。
* 資料必須包含索引鍵和值。
* 索引鍵可出現在值的上方或左邊，而不能在下方或右邊。

表單辨識器目前不支援這些類型的輸入資料：

* 複雜資料表 (巢狀資料表、合併的標題或資料格等等)。
* 核取方塊或選項按鈕。
* 超過 50 頁的 PDF 文件。
