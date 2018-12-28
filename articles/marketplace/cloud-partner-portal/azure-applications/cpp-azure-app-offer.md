---
title: Azure 應用程式供應項目 | Microsoft Docs
description: 在 Azure Marketplace 上發佈 Azure 應用程式供應項目的程序概觀。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195563"
---
# <a name="azure-application-offer"></a>Azure 應用程式供應項目

本節說明如何將新的 Azure 應用程式供應項目發佈到 <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>。
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| 每個 Azure 應用程式都包含定義應用程式所使用之所有技術資產的 Azure Resource Manager 範本，這通常會包含一部或多部虛擬機器，以及其他支援的 Azure 或 Web 型服務。 | ![Azure 應用程式圖示](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>優點

在 Microsoft Marketplace 上列出您的應用程式的部分優點包括：

* 跨 Office 365 和 Dynamics 365 觸達 1 億位 Azure Active Directory 使用者。

* 擴充您的銷售團隊：觸達全球的商務使用者，並獲得銷售管道來吸引終端使用者、協助產生潛在客戶，以及跨行業起始與新客戶的對話。

* 取得可操作的深入解析：我們將會分享您的應用程式在 AppSource 上的表現、哪些部分效果良好，以及如何進一步提升銷售程序的深入見解。

## <a name="types-of-azure-applications"></a>Azure 應用程式類型

Azure 應用程式有兩種：受控應用程式和解決方案範本。 儘管類似，但還是有一些值得注意的差異。

### <a name="solution-template"></a>解決方案範本

解決方案範本是在 Marketplace 中發佈解決方案的其中一個主要方法。 當您的解決方案除了單一虛擬機器 (VM) 之外，還需要以自動化方式進行額外的部署和設定時，可以使用此供應項目類型。 您可以使用解決方案範本，自動提供多部 VM。 這包括佈建網路和儲存體資源以提供複雜的 IaaS 解決方案。 如需解決方案範本需求和計費模型的概觀，請參閱 [Azure 應用程式：解決方案範本](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)。

### <a name="managed-application"></a>受控應用程式

受控應用程式與 Marketplace 中的解決方案範本類似，只有一個主要差異。 在受控應用程式中，資源會部署到應用程式發行者所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。 身為發行者，您可以指定解決方案持續支援的成本。 使用 Azure 受控應用程式，輕鬆建置完全受控的周全應用程式並提供給您的客戶。

除了 Marketplace，您也可以提供服務類別目錄中的受控應用程式。 服務類別目錄是組織中使用者適用的已核准解決方案的內部目錄。 您會使用目錄來符合組織標準，同時為組織中的群組提供解決方案。 員工會使用目錄輕鬆尋找其 IT 部門所建議和核准的應用程式。

如需受控應用程式的優點與類型詳細資訊，請參閱 [Azure 受控應用程式概觀](https://docs.microsoft.com/azure/managed-applications/overview)。

## <a name="publishing-overview"></a>發佈概觀

下列影片[建置 Azure Marketplace 的解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)概述如何撰寫 Azure Resource Manager 範本以定義 Azure 應用程式解決方案，以及之後如何將應用程式供應項目發佈至 Azure Marketplace。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>發佈程序工作流程

下圖顯示發佈 Azure 應用程式供應項目的概略程序。

![發佈供應項目的工作流程](./media/new-offer-process.png)

## <a name="offer-components"></a>供應項目元件

本節將概略介紹發佈受控應用程式供應項目的要素，預計要用來做為 Azure Marketplace 發佈者的指南。 發佈將分為以下幾個主要部分： 

* [必要條件](./cpp-prerequisites.md)：列出建立或發佈受控應用程式供應項目之前，需符合的技術與商業需求。 
* [建立供應項目](./cpp-create-offer.md)：提供使用 Cloud Partner 入口網站建立受控應用程式供應項目所需的步驟。 
* [發佈供應項目](./cpp-publish-offer.md)：說明如何提交供應項目以發佈至 Azure Marketplace。

## <a name="steps-in-the-publishing-process"></a>發佈程序中的步驟

發佈 Azure 應用程式供應項目的概略步驟如下：

1. 建立供應項目：提供有關供應項目的詳細資訊。 這些資訊包括：供應項目說明、行銷資料、支援資訊，以及資產規格。

2. 建立商業及技術資產：建立相關解決方案的商業資產 (法律文件和行銷資料) 及技術資產。

3. 建立 SKU：建立與供應項目相關聯的 SKU。 您需要針對預計發佈的每個映像提供個別的 SKU。

4. 認證及發佈供應項目：供應項目和技術資產完成後，便可以提交供應項目。 提交作業將啟動發佈程序。 在此程序中，解決方案會經過測試、驗證、認證，然後在 Azure Marketplace「上線」。

## <a name="next-steps"></a>後續步驟

考慮進行這些步驟之前，您必須先符合將受控應用程式發佈至 Microsoft Azure Marketplace 的[技術和商務需求](./cpp-prerequisites.md)。