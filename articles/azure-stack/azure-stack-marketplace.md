---
title: 發佈 Azure Stack (雲端操作員) 中的自訂 Marketplace 項目 | Microsoft Docs
description: 身為 Azure Stack 操作員，應了解如何發佈 Azure Stack 中的自訂 Marketplace 項目。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: df1d263b321361c00ceb37c84858dd2c62033228
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245340"
---
# <a name="azure-stack-marketplace-overview"></a>Azure Stack Marketplace 概觀

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

Azure Stack Marketplace 是針對 Azure Stack 自訂的一組服務、應用程式和資源集合。 資源包括網路、虛擬機器、儲存體等等。 使用 Marketplace 來建立新的資源和部署新的應用程式；或瀏覽和選擇您想要使用的項目。 若要使用 Marketplace 項目，使用者必須訂閱會授與他們項目存取權的供應項目。

身為 Azure Stack 操作員，可以決定要新增 (發佈) 到 Marketplace 中的項目。 您可以發佈資料庫、App Service 等項目。 發佈以後，所有的使用者都可以看到它們。 您可以發佈自己建立的自訂項目，也可以從成長中的 [Azure Marketplace 項目清單](azure-stack-marketplace-azure-items.md)內發佈項目。 當您將項目發佈到 Marketplace 中，使用者在五分鐘內就可看到它。

> [!CAUTION]  
> 在 Azure Stack Marketplace 上提供資源庫項目成品 (包括映像和 JSON 檔案) 後，便可不經驗證就存取這些成品。 如需更多在發行自訂市集項目時的考量，請參閱[建立和發行 Marketplace 項目](azure-stack-create-and-publish-marketplace-item.md)。

若要開啟 Marketplace，請在管理員入口網站中選取 [+ 建立資源]。

![Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace 項目

Azure Stack Marketplace 項目是您的使用者可下載並使用的服務、應用程式與資源。 所有的使用者都可以看到所有的 Azure Stack Marketplace 項目，包括方案和供應項目等系統管理項目。 不需要訂用帳戶即可檢視這些項目，但是對使用者而言，這些項目不具功能性。

每個 Marketplace 項目都有：

* 供資源佈建使用的 Azure 資源管理員範本。
* 中繼資料，像是字串、圖示與其他行銷關聯資料。
* 顯示入口網站中項目的格式化資訊。

發佈至 Marketplace 的每個項目都會使用 Azure 資源庫套件 (.azpkg) 格式。 請分別將部署或執行階段資源 (代碼、含軟體的 Zip 檔或虛擬機器映像) 新增到 Azure Stack 中，而不是當作 Marketplace 項目的一部分。

在版本 1803 和更新版本中，當您從 Azure 下載映像或上傳自訂映像時，Azure Stack 會將映像轉換成疏鬆檔案。 此程序會在新增影像時增加時間，但可節省空間並加速這些映像的部署。 轉換只適用於新的映像。 現有的映像不會變更。

## <a name="next-steps"></a>後續步驟

* [下載 Marketplace 項目](azure-stack-download-azure-marketplace-item.md)  
* [建立及發佈 Marketplace 項目](azure-stack-create-and-publish-marketplace-item.md)
