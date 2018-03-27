---
title: 在本教學課程中，您會了解如何更新 Azure Stack 訂閱詳情和方案 | Microsoft Docs
description: 本文說明如何檢視及修改現有的 Azure Stack 訂閱詳情和方案。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>教學課程：更新訂閱詳情和方案
身為 Azure Stack 操作人員，您可以為您的使用者建立包含所需服務和適用配額的方案，供其訂閱。 這些「基本方案」包含了要提供給您使用者的核心服務，而每個訂閱詳情都只能有一項基本方案。 若您需要修改訂閱詳情，可以使用「附加方案」，讓您可以修改方案來一開始基本方案所提供的擴充電腦、儲存體或網路配額。 

雖然在某些情況下，將所有項目結合在單一方案中可能會是最佳的做法；仍建議您先訂閱基本方案，再使用附加方案來提供其他服務。 例如，您可能會決定在基本方案中提供 IaaS 服務，而將所有 PaaS 服務當作附加方案。 方案也可用來在有限的 ASDK 環境中，控制資源耗用量。 例如，若您想讓使用者注意資源使用量，可使用相對較小的基本方案 (取決於所需的服務)；當使用者達到容量上限，系統會根據指派給他們的方案，提醒他們已耗用了配置給他們的資源。 接下來，使用者可以選取可用的附加方案來獲得額外的資源。 

> [!NOTE]
> 使用者將附加方案新增到現有訂閱詳情的訂用帳戶時，最多可能需要一小時額外的資源才會出現。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立附加方案 
> * 訂閱附加方案

## <a name="create-an-add-on-plan"></a>建立附加方案
您可以修改現有的訂閱詳情來建立「附加方案」。

1. 以雲端系統管理員身分登入 [Azure Stack 入口網站](https://adminportal.local.azurestack.external)。
2. 請遵循先前[建立基本方案](asdk-offer-services.md)時所使用的相同步驟來建立新方案，提供先前未提供的服務。 在此範例中，金鑰保存庫 (Microsoft.KeyVault) 服務將會包含在方案中。
3. 在系統管理員入口網站中，按一下 [訂閱詳情]，然後選取要以附加方案來更新的訂閱詳情。

   ![](media/asdk-update-offers/1.PNG)

4.  請捲動到訂閱詳情屬性的底部，並選取 [附加方案]。 按一下 [新增] 。
   
    ![](media/asdk-update-offers/2.PNG)

5. 選取要新增的方案。 在此範例中，方案稱為「**金鑰保存庫方案**」，請按一下 [選取]，將方案新增到訂閱詳情中。 您應該會收到通知，告訴您方案已成功新增到訂閱詳情中。
   
    ![](media/asdk-update-offers/3.PNG)

6. 請檢閱訂閱詳情所隨附的附加方案清單，確認有列出新的附加方案。
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>訂閱附加方案
您需要登入 Azure Stack 使用者入口網站，才能以附加方案來擴充現有的 Azure Stack 訂用帳戶。

您可以使用下列步驟來探索 Azure Stack 操作人員所提供的額外資源，以及將附加方案新增到您先前所訂閱的訂閱詳情中。

1. 登入[使用者入口網站](https://portal.local.azurestack.external)。
2. 若要尋找訂用帳戶以擴充附加方案，請按一下 [更多服務]、按一下 [訂用帳戶]，然後選取您的訂用帳戶。
   
    ![](media/asdk-update-offers/5.PNG)

3.  在訂用帳戶概觀中按一下 [新增方案]。
   
    ![](media/asdk-update-offers/6.PNG)

4. 在 [新增方案] 刀鋒視窗中，選取要新增到訂用帳戶中的附加方案。 在此範例中，所選取的是「**金鑰保存庫方案**」。 您應該會收到通知，告訴您已成功取得附加方案，您需要重新整理入口網站才能存取已更新的訂用帳戶。
   
    ![](media/asdk-update-offers/7.PNG)

5. 最後，請檢閱與訂用帳戶相關聯的附加方案，以確保的確已成功新增附加方案。
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立附加方案 
> * 訂閱附加方案

> [!div class="nextstepaction"]
> [利用範本建立虛擬機器](asdk-create-vm-template.md)

