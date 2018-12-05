---
title: 在 Azure Content Moderator 中審核影像 | Microsoft Docs
description: 審核工具可讓人力審核者審核影像。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/06/2017
ms.author: sajagtap
ms.openlocfilehash: 655c4b6d142c9f792a3b16c6b20d53efef51ea5b
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852821"
---
# <a name="review-moderated-images"></a>審核已審核的影像

在您註冊內容審核並取得訂用帳戶金鑰之後，您可以試用影像審核功能。

1.  開啟[人力審核工具](https://contentmoderator.cognitive.microsoft.com/)並登入。 
2.  按一下 [嘗試] 索引標籤，並上傳一些影像進行審核。
3.  按一下 [審核] 索引標籤，然後選取 [影像]。

  ![顯示審核工具的 Chrome 瀏覽器，其中 [審核影像] 選項已反白顯示](images/review-images-1.png)

  影像會與審核工具所指派的任何標籤一起顯示。 當您審核影像時，影像無法提供您小組中其他審核者使用。

4.  移動「要顯示的審核」滑桿 (1) 以調整畫面上顯示的影像數目。 按一下已標記或未標記的按鈕 (2)，據以排序影像。 按一下標記 (3) 加以開啟或關閉。

  ![顯示含已標記影像供審核之審核工具的 Chrome 瀏覽器](images/review-images-2.png)
 
5.  若要查看影像的詳細資訊，請按一下縮圖上的省略符號，後面接著 [檢視詳細資料] 選項。 若要將影像指派給子小組，請選取 [移至] 選項。
 
  ![[檢視詳細資料] 選項已反白顯示的影像](images/review-images-3.png)

6. 在詳細資料頁面上瀏覽影像審核資訊。

  ![仲裁詳細資料列在不同窗格的影像](images/review-images-4.png)
 
7.  在您視需要審核並更新標記指派後，請按 [下一步] 以提交您的審核。

在您提交之後，您大約有五秒的時間可按 [上一步] 按鈕，以返回上一個畫面並再次審核影像。 在這之後，影像不再存在於 [提交] 佇列中，而且無法再使用 [上一步] 按鈕。
