---
title: 編輯知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/06/2018
ms.author: tulasim
ms.openlocfilehash: adcefe8fed927aca2533ea811bac56f0b92288de
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279698"
---
# <a name="edit-a-knowledge-base"></a>編輯知識庫

QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。

## <a name="edit-your-knowledge-base-content"></a>編輯您的知識庫的內容

1.  在上方導覽列中選取 [我的知識庫]。 

    您可以看到您建立或與您分享的所有服務，這些服務均按照**上次修改**日期的遞減順序排列。

    ![我的知識庫](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. 選取特定的知識庫進行編輯。
 
3. 按一下 [設定] 。

   您可以在這裡編輯必要的欄位：[服務名稱]。
  
   您可以藉由按一下 [管理知識庫] -> [+ 新增 URL] 連結來新增 URL，以便將新的常見問題集內容新增至知識庫。
   
   您可以藉由按一下**刪除圖示**，來刪除現有的 URL。
   
   如果您想要讓知識庫對現有 URL 的最新內容進行編目，請選取名為 [重新整理] 的核取方塊，這樣會以最新的 URL 內容來更新知識庫。
   
您可以藉由按一下 [管理知識庫] -> [+ 新增檔案]，將支援的檔案文件新增為知識庫的一部分

您也可以藉由按一下 [匯入知識庫] 按鈕，匯入任何現有的知識庫。 
   
知識庫的更新取決於當建立與知識庫相關聯的 QnA Maker 服務時，所使用的**管理定價層**。 如有需要，您也可以從 Azure 入口網站更新管理層。

4. 變更知識庫完成之後，按一下頁面右上角的 [Save and train]\(儲存並訓練\) 維持變更。    

    ![儲存並訓練](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    未按 [Save and train]\(儲存並訓練\) 就離開此頁面將不會保留此變更。

## <a name="add-a-qna-pair"></a>加入 QnA 組

選取 [Add QnA pair]\(加入 QnA 組\) 將新的資料列加入至知識庫。

![加入 QnA 組](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>刪除 QnA 組

若要刪除 QnA，請按一下 QnA 資料列最右側的 [刪除] 圖示。

![刪除 QnA 組](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>新增替代問題

將替代問題新增至現有 QnA 組來改善與使用者查詢相符的可能性。

![新增替代問題](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>新增中繼資料


選取篩選圖示來新增中繼資料組

![新增中繼資料](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> 務必在進行編輯後定期儲存和訓練知識庫，以避免遺失變更。

## <a name="manage-large-knowledge-bases"></a>管理大型知識庫

1. QnA 是按照擷取資料的資料來源**分組**。 您可以展開或摺疊資料來源。
2. 您可以使用知識庫資料表頂端的文字方塊來**搜尋**知識庫。 按一下輸入來搜尋問題、答案或中繼資料內容。 按一下 X 圖示移除搜尋篩選條件。
3. **分頁**可讓您管理大型知識庫

    ![搜尋、分頁、群組](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="delete-knowledge-bases"></a>刪除知識庫

刪除知識庫 (KB) 是永久性的作業。 該作業無法復原。 刪除知識庫之前，您應該從 QnA Maker 入口網站的 [設定] 頁面將知識庫匯出。 

如果您與[共同作業者](collaborate-knowledge-base.md)共用知識庫然後刪除它，則每個人都會無法存取 KB。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在知識庫上共同作業](./collaborate-knowledge-base.md)
