---
title: 標準的合約 |Azure
description: Azure Marketplace 和 AppSource 中的標準合約
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620406"
---
# <a name="standard-contract"></a>標準合約

若要簡化客戶的採購程序，並降低軟體廠商的合法的複雜性，Microsoft 會提供標準的合約範本以協助在 marketplace 中的交易。 製作自訂的條款及條件，可以選擇 Azure Marketplace 發行者提供其軟體，客戶只需要檢查，並接受一次的標準契約義務。 標準的合約可以在這裡找到： [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178)。 

條款及條件供應項目上未定義 [Marketplace] 索引標籤在 Cloud Partner 入口網站中建立供應項目時。 標準的合約 選項會啟用設定變更為 是。

![啟用標準的合約 選項](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>如果您選擇使用標準的合約，都仍需要的個別條款及條件[雲端解決方案提供者](./cloud-solution-providers.md)通道。

## <a name="standard-contract-amendments"></a>標準的契約修訂文件

標準的契約修訂文件可讓發行者將選取的標準的合約，為了簡單起見，並自訂其產品或公司的條款。  客戶只需要檢閱的合約，修訂文件，如果它們已檢閱並接受 Microsoft 標準合約。

有兩種類型的修訂文件可讓 Azure Marketplace 發行者：

* 通用的修訂文件：這些修改會全域套用至所有客戶的標準合約。 通用的修訂文件會顯示產品的購買流程中的每個客戶。

![通用的修訂文件](media/marketplace-publishers-guide/universal-amendaments.png)

* 自訂的修訂文件：Azure Marketplace 也有針對自訂修訂文件，專供租用戶佈建。 也就是特殊修訂文件的標準合約以僅限特定客戶為目標。 發行者可以選擇他們想要為目標的租用戶。 從該租用戶的客戶會購買產品的標準的合約與目標的修訂文件。

![自訂的修訂文件](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>使用自訂的修訂文件為目標的客戶也會購買的標準條款的通用增修條款。

>[!Note]
>下列的供應項目類型都支援標準的契約修訂文件：Azure 應用程式 （解決方案範本與受控應用程式）、 虛擬機器、 容器、 容器應用程式。

### <a name="customer-experience"></a>客戶經驗

在 Azure 入口網站中採購過程中，客戶將能夠看到產品相關聯的修訂文件以及 Microsoft 的標準合約條款。

![Azure 入口網站的客戶體驗。](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

客戶得使用`Get-AzureRmMarketplaceTerms`擷取供應項目中的條款，並接受它。 Cmdlet 的輸出中，將會傳回標準的合約和相關聯的修訂文件。

---
