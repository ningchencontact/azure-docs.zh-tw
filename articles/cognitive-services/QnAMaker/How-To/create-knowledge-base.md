---
title: 如何建立知識庫 - QnA Maker - Azure 認知服務 | Microsoft Docs
description: 如何建立知識庫
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370878"
---
# <a name="create-a-knowledge-base"></a>建立知識庫

透過 QnA Maker，您將可非常輕鬆地加入現有的資料來源以建立知識庫。 您可以從常見問題集頁面、產品手冊、結構化文件建立新的 QnA Maker 知識庫，或在編輯方面加以新增。

## <a name="steps"></a>步驟

1. 若要開始使用，請使用您的 Azure 認證登入 [QnA Maker 入口網站](https://qnamaker.ai)，然後按一下 [建立新的服務]。

    ![建立知識庫 ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. 如果您尚未建立 QnA Maker 服務，請選取 [建立 QnA 服務]。 否則，請在步驟 2 中從下拉式清單選擇 QnA Maker 服務。 選取將裝載知識庫的 QnA Maker 服務。

    ![設定 QnA 服務](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. 輸入下列資訊以建立知識庫。

    ![設定資料來源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 您的服務提供**名稱**。 重複的名稱和特殊字元均受支援。
    - 貼上您將從中進行擷取的 URL。 您可以在[這裡](../Concepts/data-sources-supported.md)查看相關資訊，以了解支援的來源類型。
    - 或者，請上傳可從中擷取資料的檔案。 請參閱[定價資訊](https://aka.ms/qnamaker-pricing
)，以了解您可以新增多少文件。
    - 如果您想要手動新增 QnA，您可以略過連結檔案。

4. 選取 [建立] 。

    ![建立知識庫](../media/qnamaker-how-to-create-kb/create-kb.png)

5. 擷取資料需要幾分鐘的時間。

    ![擷取](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. 如果知識庫已成功建立，您將會重新導向至 [知識庫] 頁面。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [匯入知識庫](../Tutorials/migrate-knowledge-base.md)
