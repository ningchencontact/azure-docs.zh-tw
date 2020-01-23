---
title: 影片索引子和 Azure 媒體服務 v3 預設值的比較
description: 本文會比較影片索引子功能和 Azure 媒體服務 v3 預設值。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 2e2abd4ffe5a6d0a6336d811599db687da146f1e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513179"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>比較 Azure 媒體服務 v3 預設值與影片索引子 

本文將比較**影片索引子 API** 與**媒體服務 v3 API** 的功能。 

目前，[影片索引子 api](https://api-portal.videoindexer.ai/)和[媒體服務 v3 api](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)所提供的功能有重迭。 下表提供了解其差異和相似性的現行指導方針。 

## <a name="compare"></a>比較

|功能|影片索引子 API |影片分析器和音訊分析器的預設值<br/>在 Azure 媒體服務 v3 中|
|---|---|---|
|媒體深入解析|[增強](video-indexer-output-json-v2.md) |[基礎](../latest/intelligence-concept.md)|
|體驗|請參閱支援功能的完整清單︰ <br/> [概觀](video-indexer-overview.md)|僅傳回影片深入解析|
|計費|[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|法規遵循|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001)、 [iso 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018)、 [SOC 1、2、3](https://www.microsoft.com/TrustCenter/Compliance/SOC)、 [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa)、 [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp)、 [PCI](https://www.microsoft.com/trustcenter/compliance/pci)和[HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust)認證。 如需最新的更新，請流覽[影片索引子的目前認證狀態](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。|媒體服務符合多項認證。 請參閱 [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 並搜尋「媒體服務」，以確認它是否符合相關憑證。|
|免費試用|美國東部|無法使用|
|區域供應狀況|美國東部2、美國中南部、美國西部2、北歐、西歐、東南亞、東亞和澳大利亞東部。  如需最新的更新，請造訪[依區域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)提供的產品頁面。|請參閱 [Azure 狀態](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。|

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)

[媒體服務 v3 概觀](../latest/media-services-overview.md)
