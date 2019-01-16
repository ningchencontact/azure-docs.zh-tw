---
title: 虛擬機器供應項目於 Azure Marketplace 上線
description: 虛擬機器供應項目於 Azure Marketplace 上線
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ad22f1944d3fe9a088b66da4cf4df7136db497f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075325"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>虛擬機器供應項目於 Azure Marketplace 上線
=========================================================

一旦您已填入所有供應項目詳細資料，就可以發行您的供應項目並讓它於 Azure Marketplace 上線。 供應項目會經歷幾個階段。 請確定您的行銷內容和 VM 映像符合品質要求，以通過 Azure 認證並於網站上線。

![供應項目上線順序 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

讓我們更詳細地介紹這個程序，以深入了解每個步驟中發生的情況。 在此程序中，您必須採取行動，以確保您的供應項目會繼續取得進展。

<a name="publishing-process"></a>發佈程序
------------------

若要啟動發行程序，請按一下 [編輯器] 索引標籤底下的 [發行]。

![供應項目上線順序 1 - 發行](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

在 [狀態] 索引標籤之下，您會看到「發佈步驟」和供應項目的進度。

![供應項目上線順序 2 - 狀態](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

在發行程序中的任何時間點，您也可以登入並按一下 [所有供應項目] 索引標籤，以檢視任何供應項目的最新狀態。 您可以直接按一下供應項目的狀態，以查看供應項目在發佈程序中的進度詳細資料。

![供應項目上線順序 3 - 所有供應項目的狀態](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

讓我們逐步解說每個發行步驟，討論每個步驟會發生什麼情況，以及您應該估計每個步驟會需要的時間長度。

**驗證必要元件 (\<1 天)**

當您按一下 [發行] 時，將會進行自動化檢查，確保您已填入供應項目的所有必要欄位。 如果未填入所有的欄位，欄位旁邊會出現警告，您必須正確填入它，然後再按一下 [發行]。

正確地完成此步驟後，快顯視窗就會出現，提示您輸入電子郵件地址。 在剩餘的發佈程序中，您會透過此電子郵件收到發佈狀態通知。 一旦提交電子郵件地址，這個步驟就算完成。

![供應項目上線順序 4 - 發行您的供應項目](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**認證 (\<5 天)**

我們會在這個步驟中執行數個測試，以確保您的 VM 映像符合 Azure 認證的需求。 [這裡](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md)有您必須確保通過認證需求的所有指引。

由於此步驟可能需要數天的時間，所以您可登出 Cloud Partner 入口網站。 如果有您需要解決的任何錯誤，我們將傳送電子郵件通知給您。 如果所有項目均順利通過，此程序會自動移至「佈建」步驟。

**佈建 (\<1 天)**

在這個階段，我們會將您的映像複寫到所有全域 Azure 資料中心，最多可能需要一天才能完成。

**封裝和潛在客戶開發註冊 (\<1 小時)**

在這個階段，我們會將技術與行銷內容合併於網站的產品頁面。

此外，如果您設定了「潛在客戶開發」功能，我們會將測試潛在客戶傳送至您的 CRM，驗證您的 CRM 整合是否能運作。 這個步驟完成後，您會在 CRM 或 Azure 資料表中看到一筆填入假資料的記錄。 潛在客戶開發的所有文件位於[此處](./cloud-partner-portal-get-customer-leads.md)。

**供應項目可供預覽**

您會收到一封電子郵件通知，表示您的供應項目已成功完成存取預覽狀態供應項目所需的步驟。 在此步驟中，您應該預覽您的供應項目並確定一切看起來正常，而且您的 VM 正確地部署在預備環境中。

只有列入允許清單的訂用帳戶可以執行此驗證。

**發行者登出**

一旦您確認一切看起來正確無誤且可在預覽狀態正常運作，您就已準備好上線。 按一下 [狀態] 索引標籤之下的 [上線]，我們會開始讓您的供應項目於生產環境和網站上線。 從您按一下 [上線] 到供應項目於網站上線，通常需要數小時的時間。 一旦您的供應項目在網站上正式上線，我們就會傳送電子郵件通知給您。

![供應項目上線順序 5 - 上線](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**上線**

您的供應項目現在於 Azure Marketplace 和 Azure 入口網站上線，客戶將能夠在其 Azure 訂用帳戶中檢視及部署您的虛擬機器。 在任何時間點，您可以按一下 [所有供應項目] 索引標籤，查看右欄列出的所有供應項目狀態。 您可以按一下狀態，以查看供應項目的詳細發佈流程狀態。

<a name="error-handling"></a>處理錯誤
--------------

在發佈過程中，可能會遇到錯誤。 如果遇到錯誤，您會收到一封通知電子郵件，通知您發生錯誤以及後續步驟的指示。 您也可以按一下 [狀態] 索引標籤，在此過程中隨時查看錯誤。您會看到程序中發生錯誤的時間點，以及概述需要解決什麼狀況的錯誤訊息。

![供應項目上線順序 6 - 錯誤訊息](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

如果在發佈過程中遇到錯誤，您必須修正這些問題，然後按一下 [發行] 重新啟動程序。 在修正任何錯誤後重新發佈時，您必須從驗證必要元件中之發行步驟的開頭著手。

如果您在解決錯誤時遇到問題，您應該開啟支援要求，向我們的支援工程師取得協助。

![供應項目上線順序 7 - 取得支援](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>取消發佈要求
--------------------------------

您可以啟動發佈程序，並且需要取消您的要求。 您只能在發行要求觸達「發行者登出」步驟時取消發行要求。 若要取消，請按一下 [取消發行]。 發佈狀態會重設為步驟 1，若要重新發行，您應該按一下 [發佈] 並遵循狀態中的步驟。

![供應項目上線順序 8 - 狀態](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

