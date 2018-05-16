---
title: Azure 媒體服務 v3 概觀 | Microsoft Docs
description: 本文提供媒體服務的高階概觀，並提供詳細資訊的文章連結。
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: Azure 媒體服務, 串流, 廣播, 即時, 離線
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/27/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 33afe5fb24309547a7aacfcbe3b799ed413594ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-media-services-v3"></a>什麼是 Azure 媒體服務 v3？

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [第 2 版 - 正式推出](../previous/media-services-overview.md)
> * [第 3 版 - 預覽](media-services-overview.md)

> [!NOTE]
> Azure 媒體服務的最新版本處於預覽狀態，且會以 v3 稱呼它。

Azure 媒體服務是雲端式平台，可讓您建置的解決方案擁有廣播品質的視訊串流、增強協助工具和散佈、分析內容等等。 不論您是應用程式開發人員、話務中心、政府機構、娛樂公司，媒體服務都能協助您建立應用程式，為時下最熱門的行動裝置和瀏覽器上的廣大觀眾，傳遞優異品質的媒體體驗。 

## <a name="what-can-i-do-with-media-services"></a>我可以如何運用媒體服務？

媒體服務可讓您在雲端建置各種媒體工作流程，以下範例是使用媒體服務可以完成的項目。  

* 以各種格式傳遞影片，使它們能在多種瀏覽器和裝置上播放。 不論是隨選播放或是即時串流傳遞至各種用戶端 (行動裝置、電視、電腦等)，視訊和音訊內容都必須適當地編碼和封裝。 若要了解如何傳遞和串流這類內容，請參閱[快速入門：編碼和串流檔案](stream-files-dotnet-quickstart.md)。
* 將即時體育賽事串流至廣大的線上觀眾，例如足球、棒球、大專院校和高中體育競賽等等。 
* 廣播公眾會議和活動，例如市政府、市議會會議，及立法機構。
* 分析錄製的視訊或音訊內容。 例如，為了達到更高的客戶滿意度，組織可以擷取語音轉換文字，並建置搜尋索引和儀表板。 接著便可以從一般客訴、客訴來源及其他相關資料中獲取情報。 
* 建立訂閱影片服務，並在客戶需要限制對著作權所有作品的存取及使用時，串流受 DRM 保護的內容。
* 傳遞離線內容，以在飛機、火車和汽車上播放。 當客戶預期會從網路中斷連線時，他們可能需要將內容下載到手機或平板電腦上播放。
* 在影片加入標題和字幕，以建立更廣大的觀眾 (例如聽障人士或想要以其他語言閱讀的人)。 
* 使用 Azure 媒體服務和 [Azure 認知服務 API](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) 的語音文字轉換、翻譯成多語言等功能，實作教育電子化學習影片平台。
* 啟用 Azure CDN 來達成大規模調整，以更妥善處理瞬間高負載 (例如，在產品上市活動開始時。) 

## <a name="v3-capabilities"></a>v3 功能

v3 是以統一的 API 表面為基礎，公開建置於 **Azure Resource Manager** 上的管理和操作功能。 此版本提供以下功能：  

* **轉換**可協助您定義媒體處理或分析工作的簡單工作流程。 轉換是處理您的視訊和音訊檔案的配方。 然後藉由將工作提交至轉換，您可以重複地套用它來處理內容庫中的所有檔案。
* **工作**可處理 (編碼或分析) 您的視訊。 可以使用 HTTP(S) URL、SAS URL 或 Azure Blob 儲存體中的檔案路徑，在工作上指定輸入的內容。 
* **通知**可監視工作進度或狀態，或直播頻道開始/停止及錯誤事件。 通知是與 Azure 事件方格通知系統整合。 您輕鬆就能在 Azure 媒體服務中的數個資源上訂閱活動。 
* **Azure Resource Management** 範本可用於建立及部署轉換、串流端點、頻道等等。
* **角色型存取控制**可以設定在資源層級，讓您能鎖定對特定資源 (如轉換、頻道等) 的存取。
* **用戶端 SDK** 有多種語言：.NET、.NET Core、Python、Go、Java 和 Node.js。

## <a name="how-can-i-get-started-with-v3"></a>如何開始使用 v3？

身為開發人員，您可以使用媒體服務 [REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) \(英文\)，或是可讓您與 REST API 互動的用戶端程式庫，輕鬆建立、管理及維護自訂媒體工作流程。 Microsoft 產生並支援以下用戶端程式庫： 

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [.NET 語言](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)
* .NET Core 
* Java

  將下列相依性新增到您的專案中：
  
  ```
  <dependency>
    <groupId>com.microsoft.azure.media-2018-03-30-preview</groupId>
    <artifactId>azure-mgmt- media</artifactId>
    <version>0.0.1-beta</version>
  </dependency> 
  ```
* Node.js 

  使用下列命令：
  
  ```
  npm install azure-arm-mediaservices
  ```
  
* [Python](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)
* [Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)

媒體服務提供 [Swagger 檔案](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) \(英文\)，可用來產生適用於您的慣用語言/技術的 SDK。  

## <a name="next-steps"></a>後續步驟

若要了解開始撰寫程式碼並串流視訊檔案是多麼容易，請查看[串流檔案](stream-files-dotnet-quickstart.md)。 

