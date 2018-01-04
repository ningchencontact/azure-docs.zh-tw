---
title: "將內部支援陳述式加入至實驗室環境中 Azure DevTest Labs |Microsoft 文件"
description: "了解如何在 Azure DevTest Labs 張貼到實驗室的內部支援陳述式"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: v-craic
ms.openlocfilehash: 33c2f1e9d1ffa83003b7f0dbba46a7a4588dd57a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>將內部支援陳述式加入至實驗室環境中 Azure DevTest Labs

Azure 的 DevTest Labs 可讓您自訂您的實驗室使用內部支援陳述式，可讓使用者在實驗室的支援資訊。 例如，您可以提供連絡資訊，讓使用者知道如何在需要協助以進行疑難排解，或存取在實驗室中的資源時，連線到內部支援。 您也可以提供連結至內部網站或您的小組可以存取，再連絡支援人員的常見問題集。

內部支援陳述式被為了讓您張貼通常不太常變更的實驗室資訊。 要通知使用者是多暫存性質 – 例如實驗室原則-最近的更新中的實驗室資訊，請參閱[在實驗室中的文章公告](devtest-lab-announcements.md)。

您可以輕鬆地停用，或編輯支援陳述式之後不再適用。

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>若要將支援的陳述式加入至現有的實驗室的步驟

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 視需要選取 [所有服務]，然後從清單中選取 [DevTest Labs]。 (您的實驗室可能已顯示在 [所有資源] 下方的儀表板上)。
1. 從清單的實驗室中，選取您要新增的支援聲明的實驗室。  
1. 在實驗室的 [概觀] 區域中選取 [組態和原則]。  

    ![組態和原則按鈕](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. 在左側**設定**，選取**內部支援**。

    ![內部支援按鈕](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. 若要建立之使用者的內部支援訊息在這個實驗室中，設定為已啟用**是**。

1. 在**支援訊息**欄位中，輸入您想要呈現給您的實驗室使用者的內部支援陳述式。 支援訊息接受 Markdown。 當您輸入的訊息文字，您可以檢視**預覽**以查看訊息給使用者的顯示畫面底部的區域。

    ![若要建立訊息的內部支援畫面。](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. 選取**儲存**一旦準備好張貼您的支援聲明。

當您不再想要顯示這個支援訊息給實驗室使用者時，返回**內部支援**頁面，然後設定**啟用**至**否**。

## <a name="steps-for-users-to-view-the-support-message"></a>若要檢視支援訊息的使用者的步驟

1. 從 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)選取一個實驗室。

1. 在本實驗室**概觀**區域中，選取**內部支援**。  

    ![內部支援按鈕](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. 如果支援訊息回傳時，使用者可以檢視從內部支援 窗格。

    ![顯示支援訊息公佈的內部支援 窗格](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
* 內部支援陳述式通常用來提供不會經常變更的支援資訊。 您也可以了解如何[張貼到實驗室公告](devtest-lab-announcements.md)以通知使用者暫時變更或更新到實驗室。
* [設定原則及排程](devtest-lab-set-lab-policy.md)提供有關如何套用其他限制和慣例您訂用帳戶使用自訂的原則資訊。