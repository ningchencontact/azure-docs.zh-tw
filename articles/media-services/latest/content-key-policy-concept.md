---
title: 媒體服務中的內容金鑰原則 - Azure | Microsoft Docs
description: 本文解釋內容金鑰原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 541713677184d93eb78856e3c3373ab432d5f0cf
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141521"
---
# <a name="content-key-policies"></a>內容金鑰原則

您可以使用 Azure 媒體服務來保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。

Azure 媒體服務 v3 中，內容金鑰原則可讓您指定如何透過媒體服務金鑰傳遞元件，將內容金鑰傳送給終端用戶端。 如需詳細資訊，請參閱[內容保護概觀](content-protection-overview.md)。

## <a name="contentkeypolicies-definition"></a>ContentKeyPolicies 定義

下表說明 ContentKeyPolicy 的屬性並提供其定義。

|Name|說明|
|---|---|
|id|資源的完整資源識別碼。|
|name|資源名稱。|
|properties.created |原則的建立日期|
|properties.description |原則的說明。|
|properties.lastModified|原則的上次修改日期|
|properties.options |金鑰原則選項。|
|properties.policyId    |舊版原則識別碼。|
|type   |資源類型。|

如需完整定義，請參閱[內容金鑰原則 (英文)](https://docs.microsoft.com/rest/api/media/contentkeypolicies)。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

媒體服務支援下列 ContentKeyPolicies 的 OData 查詢選項： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

運算子說明：

* Eq = 等於
* Ne = 不等於
* Ge = 大於或等於
* Le = 小於或等於
* Gt = 大於
* Lt = 小於

### <a name="filteringordering"></a>篩選/排序

下表說明這些選項可如何套用至 StreamingPolicy 屬性： 

|Name|Filter|順序|
|---|---|---|
|id|||
|name|Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.created |Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.description |Eq、ne、ge、le、gt、lt||
|properties.lastModified    |Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.options |||
|properties.policyId    |Eq、ne||
|type   |||

### <a name="pagination"></a>分頁

在四個已啟用的分類排序中，每一個皆可支援分頁。 目前的頁面大小為 10。

> [!TIP]
> 您應一律使用下一個連結來列舉集合，而不應依存於特定頁面大小。

如果查詢回應包含許多項目，服務會傳回 "\@odata.nextLink" 屬性，以取得下一頁的結果。 這可用來逐頁查看整個結果集。 您無法設定頁面大小。 

如果在逐頁查看集合時建立或刪除了 StreamingPolicy，則所做的變更會反映在傳回的結果中 (如果這些變更屬於尚未下載的集合)。 

下列 C# 範例說明如何列舉帳戶中的所有 ContentKeyPolicies。

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

如需 REST 範例，請參閱[內容金鑰原則 - 清單 (英文)](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="next-steps"></a>後續步驟

[使用 AES-128 動態加密和金鑰傳遞服務](protect-with-aes128.md)

[使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
