---
title: 邀請使用者到影片索引子-Azure
titlesuffix: Azure Media Services
description: 本文說明如何邀請使用者加入影片索引子。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: 718d69f8b582d8d31ca0abb1867eb0560b0dc53b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827977"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>快速入門：邀請使用者加入影片索引子

若要與同事共同作業，您可以邀請他們前往您的影片索引子帳戶。 

> [!NOTE]
> 只有帳戶的系統管理員可以新增或移除使用者。

## <a name="invite-new-users"></a>邀請新使用者

1. 登入 [Video Indexer](https://www.videoindexer.ai/) 網站。 請確定您已使用系統管理員帳戶連線。
1. 按一下頂端功能表中的 [**邀請其他人**] 按鈕：

   ![邀請新使用者](./media/invite-users/invite-users.png)

1. 將您想要新增的人員的電子郵件地址新增至您的影片索引子帳戶：

    ![邀請使用者加入此帳戶](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > 您邀請的所有使用者都將擁有您帳戶中所有影片的 [讀取] 和 [寫入] 許可權。
1. 您邀請的使用者將會收到包含連結的電子郵件，而且只要按一下 [**加入影片索引子**] 連結，就能夠存取該帳戶：

    ![確認](./media/invite-users/invite-msg.png)

    使用者必須按一下連結以加入，才能取得帳戶的存取權。 

## <a name="removing-existing-users"></a>移除現有的使用者

如果您想要移除具有帳戶存取權的使用者，您可以按一下其名稱旁邊的**X**符號：

![移除使用者](./media/invite-users/remove-users.png)

使用者不會在移除時收到通知。 移除之後，使用者將不會被授權登入。

## <a name="next-steps"></a>後續步驟

您現在可以使用[影片索引子網站](video-indexer-view-edit.md)或[影片索引子開發人員入口網站](video-indexer-use-apis.md)來查看影片的見解。

## <a name="see-also"></a>另請參閱

- [影片索引子概觀](video-indexer-overview.md)
- [如何註冊及上傳您的第一段影片](video-indexer-get-started.md)
- [開始使用 Api](video-indexer-use-apis.md)
