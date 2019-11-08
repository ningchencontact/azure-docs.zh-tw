---
title: 標準合約 |Azure
description: Azure Marketplace 和 AppSource 中的標準合約
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819503"
---
# <a name="standard-contract"></a>標準合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜度，Microsoft 提供了標準合約範本，以協助在 marketplace 中進行交易。 Azure Marketplace 發行者可以選擇在標準合約下提供其軟體，而不是製作自訂的條款及條件，而是客戶只需要審查並接受一次。 您可以在這裡找到標準合約： [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)。 

在 Cloud Partner 入口網站中建立供應專案時，會在 [Marketplace] 索引標籤上定義供應專案的條款及條件。 將設定變更為 [是]，即可啟用 [標準合約] 選項。

![啟用標準合約選項](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>如果您選擇使用標準合約，則[雲端解決方案提供者](./cloud-solution-providers.md)通道仍然需要個別的條款及條件。

## <a name="standard-contract-amendments"></a>標準合約修訂

標準合約修訂可讓發行者針對簡單性選取標準合約，並使用其產品或企業的自訂詞彙。  如果客戶已審查並接受 Microsoft 標準合約，則只需要審查合約的修訂。

Azure Marketplace 發行者有兩種可用的改正：

* 通用修訂：這些修訂通用適用于所有客戶的標準合約。 在購買流程中，產品的每個客戶都會看到通用修訂。

![通用改正](media/marketplace-publishers-guide/universal-amendaments.png)

* 自訂改正： Azure Marketplace 也具有以租使用者為目標的自訂修訂的布建。 它們對於僅以特定客戶為目標的標準合約而言，是特別的改正。 發行者可以選擇想要設為目標的租使用者。 來自該租使用者的客戶會在標準合約和目標修訂下購買產品。

![自訂改正](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>以自訂修訂為目標的客戶，也會在購買期間取得標準條款的通用修訂。

>[!Note]
>下列供應專案類型支援標準合約改正： Azure 應用程式（解決方案範本和受控應用程式）、虛擬機器、容器、容器應用程式。

### <a name="customer-experience"></a>客戶體驗

在 Azure 入口網站的購買過程中，客戶將能夠查看與產品相關聯的條款，做為 Microsoft 標準合約和修訂。

![Azure 入口網站的客戶體驗。](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

客戶可以使用 `Get-AzureRmMarketplaceTerms` 來取得供應專案的條款並接受。 標準合約和相關的改正將會在 Cmdlet 的輸出中傳回。

---
