---
title: 影片索引子與 Azure 媒體服務 v3 預設值的比較 | Microsoft Docs
description: 本主題會比較影片索引子與 Azure 媒體服務 v3 的預設值。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270331"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>比較 Azure 媒體服務 v3 預設值與影片索引子 

本文將比較**影片索引子 API** 與**媒體服務 v3 API** 的功能。 

目前沒有所提供的功能之間重疊[影片索引子 Api](https://api-portal.videoindexer.ai/)並[媒體服務 v3 Api](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)。 下表提供了解其差異和相似性的現行指導方針。 

## <a name="compare"></a>比較

|功能|影片索引子 API |影片分析器和音訊分析器的預設值<br/>在 Azure 媒體服務 v3 中|
|---|---|---|
|媒體深入解析|[增強](video-indexer-output-json-v2.md) |[基礎](../latest/intelligence-concept.md)|
|體驗|請參閱支援功能的完整清單︰ <br/> [概觀](video-indexer-overview.md)|僅傳回影片深入解析|
|計費|[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|法規遵循|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001)， [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018)， [SOC 1 2、 3](https://www.microsoft.com/TrustCenter/Compliance/SOC)， [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa)， [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp)， [PCI](https://www.microsoft.com/trustcenter/compliance/pci)，以及[HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust)認證。 如需最新的更新，造訪[影片索引器的目前認證狀態](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。|媒體服務符合多項認證。 請參閱 [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 並搜尋「媒體服務」，以確認它是否符合相關憑證。|
|免費試用|美國東部|尚未提供|
|區域可用性|美國東部 2、 美國中南部、 美國西部 2、 北歐、 西歐、 東南亞、 東亞、 和澳大利亞東部。  如需最新的更新，造訪[依區域的產品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)頁面。|請參閱 [Azure 狀態](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。|

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)

[媒體服務 v3 概觀](../latest/media-services-overview.md)
