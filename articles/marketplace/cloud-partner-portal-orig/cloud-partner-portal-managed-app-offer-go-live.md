---
title: Azure 應用程式供應項目於 Azure Marketplace 上線 | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 18a8e6ae8ab3bd4299c6a014f938e73a2a021492
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263582"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>Azure 應用程式供應項目於 Azure Marketplace 上線 
===========================================================

您現在已填入所有供應項目詳細資料，現在可以發行您的供應項目並讓它於 Azure Marketplace 上線。 有幾個步驟，以確定您的行銷內容和應用程式在網站上線之前，符合 Azure 認證的品質要求。

![發行流程](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

讓我們更詳細地解說這個程序，以深入了解每個步驟會發生什麼狀況，以及您在此程序中需要哪些動作項目，進而確保您的供應項目會繼續進行。

<a name="publishing-process"></a>發佈程序 
------------------

按一下 [編輯器] 索引標籤底下的 [發行]\"\"，即可啟動發行程序。

![供應項目上線順序 1 - 發行](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

在 [狀態] 索引標籤之下，您會看到「發行步驟」和供應項目位於哪個步驟。

![供應項目上線順序 2 - 工作流程](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

在發行程序中的任何時間點，您也可以登入並按一下 [所有供應項目] 索引標籤，以檢視任何供應項目的最新狀態。 您可以直接按一下供應項目的狀態，以查看供應項目在發佈程序中的進度詳細資料。

> [!WARNING]
> 如果客戶使用狀況屬性未啟用，您就會在發行程序期間收到下列錯誤訊息：「一或多個 Azure Resource Manager 範本中遺漏 Azure 合作夥伴客戶使用狀況屬性。 若要解決，請在下列方案的 mainTemplate.json 套件中，將追蹤 GUID 新增至其中的 Azure Resource Manager 範本：servicenow。 如需詳細資訊，請參閱 http://aka.ms/customerusageattribution。」 

讓我們逐步解說每個發行步驟，並討論每個步驟會發生什麼情況，以及您應該估計每個步驟會需要的時間長度。

### <a name="validate-prerequisites-1-day"></a>驗證必要條件 (\<1 天) 

當您按一下 [發行] 時，將會進行自動化檢查，確保您已填入供應項目的所有必要欄位。 如果未填入所有的欄位，欄位旁邊會出現警告，您必須正確填入它，然後再按一下 [發行]。

正確地完成此步驟後，快顯視窗就會出現，要求您提供電子郵件地址。 在剩餘的發佈程序中，您會透過此電子郵件收到發佈狀態通知。 一旦提交電子郵件地址，這個步驟就算完成。

![供應項目上線順序 1 - 發行 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>認證 (\<5 天) 

我們會在這個步驟中執行數個測試，以確保您的 Azure 應用程式套件符合 Azure 認證的需求。

由於此步驟可能需要數天的時間，所以您可登出 Cloud Partner 入口網站。 如果有任何錯誤，我們將傳送電子郵件通知給您。 如果所有項目均順利通過，此程序會自動移至「佈建」步驟。

### <a name="packaging-and-lead-generation-registration-1-hour"></a>封裝和潛在客戶產生註冊 (\<1 小時) 

在這個階段，我們會將技術與行銷內容合併於網站的產品頁面。

### <a name="offer-available-in-preview"></a>供應項目可供預覽 

您會收到一封電子郵件通知，表示您的供應項目已成功完成存取預覽狀態供應項目所需的步驟。 在此步驟中，您應該預覽您的供應項目，並確定一切看起來很正常。 請檢查您的 VM 是否已正確部署在預備環境中。

只有列入允許清單的訂用帳戶可以執行此驗證。\*

### <a name="publisher-sign-out"></a>發行者登出 

一旦您確認一切看起來正確無誤且可在預覽狀態正常運作，您就已準備好上線。 按一下 [狀態] 索引標籤之下的 [上線]，我們會開始讓您的供應項目於生產環境和網站上線。 從您按一下 [上線] 到供應項目於網站上線，通常需要數小時的時間。 一旦您的供應項目在網站上正式上線，我們就會傳送電子郵件通知給您。

![供應項目上線順序 6 - 上線](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Microsoft 檢閱 

當您準備就緒，按一下 [上線]，將在 Visual Studio Online 中建立工作站，接著對套件內容進行 Microsoft 程式碼檢閱。 程式碼檢閱將涵蓋在撰寫範本、秘訣和訣竅以最佳化建立資源時所使用的最佳模式/作法。 如果因為發行者進行檔案變更而產生意見反應，發行程序必須重新啟動。 目前的發行將會取消，您必須重新發行，以解決意見反應註解。

### <a name="live"></a>即時

您的供應項目現在已在 Azure Marketplace 和 Azure 入口網站上線。 您的客戶將能夠在其 Azure 訂用帳戶中檢視及部署您的 Azure 受控應用程式。 您可以按一下 [所有供應項目] 索引標籤，查看右欄列出的所有供應項目狀態。 您可以按一下狀態，以查看供應項目的詳細發佈流程狀態。

### <a name="error-handling"></a>錯誤處理 

如果遇到錯誤，您會收到一封通知電子郵件，通知您發生錯誤以及後續步驟的指示。 您也可以按一下 [狀態] 索引標籤，在此過程中隨時查看錯誤。您會看到程序中發生錯誤的時間點，以及概述需要解決什麼狀況的錯誤訊息。

如果在發行過程中遇到錯誤，您必須修正這些錯誤，然後按一下 [發行] 重新啟動程序。 在修正任何錯誤後重新發佈時，您必須從發佈步驟中「驗證必要元件」的開頭著手。

如果您在解決錯誤時遇到問題，您應該開啟支援要求，向我們的支援工程師取得協助。

### <a name="canceling-the-publishing-request"></a>取消發佈要求

您可以啟動發佈程序，並且需要取消您的要求。 您只能在發佈要求觸達「發行者登出」步驟時取消發佈要求。 若要取消，請按一下 [取消發行]\'\'。 發佈狀態會重設為步驟 1，若要重新發行，您應該按一下 [發佈] 並遵循狀態中的步驟。
