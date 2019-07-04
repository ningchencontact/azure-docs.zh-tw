---
title: 知識庫-QnA Maker 的共同作業
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓多位使用者對知識庫進行共同作業。 這項功能是連同 Azure 角色型存取控制一起提供。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 757b791f632e6d7ecb15a0a40384061f0658a6ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447523"
---
# <a name="collaborate-on-your-knowledge-base"></a>對知識庫進行共同作業

QnA Maker 可讓多位使用者對知識庫進行共同作業。 這項功能是連同 Azure [角色型存取控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)一起提供。 

執行下列步驟，與他人共用 QnA Maker 服務：

1. 登入 Azure 入口網站中，並移至您的 QnA Maker 資源。

    ![QnA Maker 資源清單](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. 移至 [存取控制 (IAM)]  索引標籤。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. 選取 [新增]  。

    ![QnA Maker IAM 新增](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. 選取**擁有者**或**參與者**角色。 您無法透過角色型存取控制授與唯讀存取。 擁有者和參與者角色針對 QnA Maker 服務具有讀取-寫入權限。

    ![QnA Maker IAM 新增角色](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. 輸入您想要分享的電子郵件，並按下 [儲存]。

    ![QnA Maker IAM 新增電子郵件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

與您分享 QnA Maker 服務的人登入 [QnA Maker 入口網站](https://qnamaker.ai)時，這些人會看見該服務中的所有知識庫。

請記住，您無法共用 QnA Maker 服務中特定一個知識庫。 如果您想要更細部的存取控制，請考慮在不同的 QnA Maker 服務發佈您的知識庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [測試知識庫](./test-knowledge-base.md)
