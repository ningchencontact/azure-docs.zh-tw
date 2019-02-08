---
title: Azure 媒體服務實體概觀 - Azure | Microsoft Docs
description: 本文提供 Azure 媒體服務實體的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3f3322245983508e374d081e5d7905f67344ad7a
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912641"
---
# <a name="azure-media-services-entities-overview"></a>Azure 媒體服務實體概觀

本文提供「Azure 媒體服務」實體的簡短概觀，並指出可讓您詳細了解每個實體在「媒體服務」工作流程中使用方式的文章。 

| 話題 | 說明 |
|---|---|
| [帳戶篩選器和資產篩選器](filters-dynamic-manifest-overview.md)|當提供您的內容給客戶 (串流處理即時事件或點播視訊) 時，您的用戶端可能需要比預設資產資訊清單檔案中所述還大的彈性。 Azure 媒體服務可讓您定義[帳戶篩選器](https://docs.microsoft.com/rest/api/media/accountfilters)與[資產篩選器](https://docs.microsoft.com/rest/api/media/assetfilters)。 然後，根據預先定義的篩選器使用**動態資訊清單**。 |
| [資產](assets-concept.md)|[資產](https://docs.microsoft.com/rest/api/media/assets)實體包含數位檔案 (包括視訊、音訊、影像、縮圖集合、文字播放軌和隱藏式輔助字幕檔案)，以及這些檔案的相關中繼資料。 將數位檔案上傳到資產之後，可以用於媒體服務編碼、串流和分析內容工作流程。|
| [內容金鑰原則](content-key-policy-concept.md)|您可以使用媒體服務來保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。|
| [實況活動與實況輸出](live-events-outputs-concept.md)|媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要在媒體服務 v3 設定即時串流事件，您必須了解[即時事件](https://docs.microsoft.com/rest/api/media/liveevents)和[即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)。|
| [串流端點](streaming-endpoint-concept.md)|[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)實體代表可以直接將內容傳遞給用戶端播放程式應用程式，或傳遞給內容傳遞網路 (CDN) 進行進一步發佈的串流服務。 來自串流端點服務的輸出串流可以是即時資料流，或媒體服務帳戶中的隨選視訊資產。 當您建立媒體服務帳戶時，系統會為您建立**預設**串流端點 (處於已停止狀態)。 您無法刪除**預設**串流端點。 您可以在帳戶下建立額外的串流端點。 若要開始串流處理視訊，您需要啟動串流端點，以便從中串流處理視訊。 |
| [串流定位器](streaming-locators-concept.md)|您必須為您的用戶端提供可用來播放已編碼的視訊或音訊檔案的 URL，您必須建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)並建置串流 URL。|
| [串流原則](streaming-policy-concept.md)| [串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)可讓您設定串流定位器的串流通訊協定和加密選項。 您可以指定您所建立自訂串流原則的名稱，或使用媒體服務提供的其中一個預先定義串流原則。 <br/><br/>使用自訂的串流原則時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，對串流定位器重新使用這些原則。 不建議您對每個串流定位器建立新的串流原則。|
| [轉換與作業](transforms-jobs-concept.md)|使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。<br/><br/>[作業](https://docs.microsoft.com/rest/api/media/jobs)是要媒體服務將已建立的**轉換**套用至指定輸入視訊或音訊內容的實際要求。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用下列項目指定輸入影片的位置：HTTPS URL、SAS URL 或資產。|

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)
