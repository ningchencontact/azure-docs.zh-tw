---
title: Azure 媒體服務 v3 版本資訊 | Microsoft Docs
description: 為了讓您隨時掌握最新的開發訊息，本文提供 Azure 媒體服務 v3 最新資訊。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 09f9731f78093354f324f263899519bc5d31fa5d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760774"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure 媒體服務 v3 版本資訊

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="known-issues"></a>已知問題

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-sdk)、CLI 或其中一個支援的 SDK。

如需詳細資訊，請參閱[從媒體服務 v2 移至 v3 的移轉指導](migrate-from-v2-to-v3.md#known-issues)。

## <a name="april-2019"></a>2019 年 4 月

[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)已新增至內建分析器預設值。

## <a name="march-2019"></a>2019 年 3 月

動態封裝現在支援 Dolby Atmos. 如需詳細資訊，請參閱 <<c0> [ 音訊轉碼器支援動態封裝](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)。

您現在可以指定資產或帳戶會套用到您的串流定位器的篩選器清單。 如需詳細資訊，請參閱 <<c0> [ 串流定位器相關聯的篩選器](filters-concept.md#associate-filters-with-streaming-locator)。

## <a name="february-2019"></a>2019 年 2 月

媒體服務 v3 現已支援在 Azure 的國家/地區雲端中。 尚未在所有雲端中提供所有功能。 如需詳細資訊，請參閱[存在 Azure 媒體服務 v3 的雲端和區域](azure-clouds-regions.md)。

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) 事件已被新增至適用於媒體服務的 Azure 事件格線結構描述。

## <a name="january-2019"></a>2019 年 1 月

### <a name="media-encoder-standard-and-mpi-files"></a>媒體編碼器標準與 MPI 檔案 

使用「媒體編碼器標準」來編碼以產生 MP4 檔案時，會產生新的 .mpi 檔案並將該檔案新增到輸出資產。 此 MPI 檔案的目的是用來改進[動態封裝](dynamic-packaging-overview.md)與串流處理案例的效能。

您不應該修改或移除該 MPI 檔案，也不應該在您的服務中相依於此類檔案的存在與否。

## <a name="december-2018"></a>2018 年 12 月

來自 V3 API GA 版本的更新包括：
       
* **PresentationTimeRange** 屬性不再是**資產篩選**和**帳戶篩選**的「必要」項目。 
* **Jobs** 和 **Transforms** 的 $Top 和 $skip 查詢選項已移除，並新增了 $orderby。 在加入新的排序功能時，我們發現以前 $top 和 $skip 選項 (即使並未實作) 會意外公開。
* 已重新啟用列舉擴充性。 此功能已在 SDK 的預覽版本中啟用，但在 GA 版本中意外停用。
* 兩個預先定義的串流原則已重新命名。 **SecureStreaming** 現在重新命名為 **MultiDrmCencStreaming**。 **SecureStreamingWithFairPlay** 現在重新命名為 **Predefined_MultiDrmStreaming**。

## <a name="november-2018"></a>2018 年 11 月

CLI 2.0 模組現已適用於 [Azure 媒體服務 v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50。

### <a name="new-commands"></a>新的命令

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - 可讓您管理媒體保留單位。 如需詳細資訊，請參閱[調整媒體保留單位](media-reserved-units-cli-how-to.md)。

### <a name="new-features-and-breaking-changes"></a>新功能和重大變更

#### <a name="asset-commands"></a>資產命令

- 已新增 ```--storage-account``` 和 ```--container``` 引數。
- 已在 ```az ams asset get-sas-url``` 命令中新增到期時間 (現在 + 23 小時) 和權限 (讀取) 的預設值。

#### <a name="job-commands"></a>作業命令

- 已新增 ```--correlation-data``` 和 ```--label``` 引數
- ```--output-asset-names``` 已重新命名為 ```--output-assets```。 現在它會接受以空格分隔且格式為 'assetName=label' 的資產清單。 可以傳送沒有標籤的資產，例如：'assetName='。

#### <a name="streaming-locator-commands"></a>串流定位器命令

- ```az ams streaming locator``` 基底命令已取代為 ```az ams streaming-locator```。
- 已新增 ```--streaming-locator-id``` 和 ```--alternative-media-id support``` 引數。
- 已更新 ```--content-keys argument``` 引數。
- ```--content-policy-name``` 已重新命名為 ```--content-key-policy-name```。

#### <a name="streaming-policy-commands"></a>串流原則命令

- ```az ams streaming policy``` 基底命令已取代為 ```az ams streaming-policy```。
- 已在 ```az ams streaming-policy create``` 中新增加密參數支援。

#### <a name="transform-commands"></a>轉換命令

- ```--preset-names``` 引數已取代為 ```--preset```。 現在您一次只能設定 1 個輸出/預設 (以新增更多必須執行 ```az ams transform output add``` 的項目)。 此外，您可傳遞自訂 JSON 的路徑以設定自訂 StandardEncoderPreset。
- 傳遞要移除的輸出索引即可執行 ```az ams transform output remove```。
- 已在 ```az ams transform create``` 和 ```az ams transform output add``` 命令中新增 ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 引數。

## <a name="october-2018---ga"></a>2018 年 10 月 - GA

本節說明 Azure 媒體服務 (AMS) 的 10 月更新。

### <a name="rest-v3-ga-release"></a>REST v3 GA 版本

[REST v3 GA 版本](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) 包含更多 API，適用於即時、帳戶/資產層級資訊清單篩選器，以及 DRM 支援。

#### <a name="azure-resource-management"></a>Azure 資源管理 

Azure 資源管理支援可提供統一的管理和作業 API (現在所有項目都在單一位置)。

從這個版本開始，您可以使用 Resource Manager 範本來建立即時事件。

#### <a name="improvement-of-asset-operations"></a>改進資產作業 

引進了下列改進︰

- 從 HTTP(s) 的 URL 或 Azure Blob 儲存體 SAS URL 內嵌。
- 指定您自己資產的容器名稱。 
- 使用 Azure Functions 建立自訂工作流程時有更簡便的輸出支援。

#### <a name="new-transform-object"></a>新的轉換物件

新的**轉換**物件會簡化編碼模型。 新的物件可讓您輕鬆地建立及共用編碼 Resource Manager 範本和預設值。 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory 驗證和 RBAC

Azure AD 驗證和角色型存取控制 (RBAC) 會啟用 Azure AD 中的安全轉換、即時事件、內容金鑰原則，或是依角色或使用者的資產。

#### <a name="client-sdks"></a>用戶端 SDK  

媒體服務 v3 中支援的語言：.NET Core、Java、Node.js、Ruby、Typescript、Python、Go。

#### <a name="live-encoding-updates"></a>即時編碼更新

導入下列即時編碼更新：

- 新的即時低延遲模式 (10 秒端對端)。
- 改進的 RTMP 支援 (更高的穩定性及更多來源編碼器支援)。
- RTMPS 安全內嵌。

    當您建立即時事件時，現在會取得 4 個內嵌 URL。 4 個內嵌 URL 幾乎完全相同，並有相同的串流權杖 (AppId)，只有連接埠號碼部分不同。 其中兩個 URL 是 RTMPS 的主要部分和備份。 
- 24 小時制的轉碼支援。 
- 已改善透過 SCTE35 在 RTMP 中執行的廣告訊號支援。

#### <a name="improved-event-grid-support"></a>已改善事件方格支援

您可以看到下列的事件方格支援增強功能：

- 整合 Azure 事件方格，以更輕鬆地使用 Logic Apps 與 Azure Functions 進行開發。 
- 訂閱編碼、即時頻道等更多的事件。

### <a name="cmaf-support"></a>CMAF 支援

Apple HLS (iOS 11 +) 的 CMAF 和 'cbcs' 加密支援和支援 CMAF 的 MPEG-DASH 播放程式。

### <a name="video-indexer"></a>影片索引子

我們已在 8 月發表了影片索引器 GA 版本。 如需目前支援功能的全新詳細資訊，請參閱[什麼是影片索引器](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)。 

### <a name="plans-for-changes"></a>方案變更

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Azure CLI 2.0 模組即將推出，內含所有功能的操作 (包括即時、內容金鑰原則、帳戶/資產篩選器、串流原則)。 

### <a name="known-issues"></a>已知問題

只有使用適用於資產或帳戶篩選器預覽 API 的客戶會受到下列問題影響。

如果您在 09/28 到 10/12 之間使用媒體服務 v3 CLI 或 API 來建立資產或帳戶篩選器，由於版本衝突，您必須移除所有的資產和帳戶篩選器並加以重新建立。 

## <a name="may-2018---preview"></a>2018 年 5 月 - 預覽

### <a name="net-sdk"></a>.NET SDK

.NET SDK 中的下列功能︰

* **轉換**和**工作**，可編碼或分析媒體內容。 如需範例，請參閱[串流處理檔案](stream-files-tutorial-with-api.md)和[分析](analyze-videos-tutorial-with-api.md)。
* **串流定位器**，用於將內容發佈及串流處理到終端使用者裝置
* **串流原則**和**內容金鑰原則**，可在傳遞內容時設定金鑰傳遞和內容保護 (DRM)。
* **即時事件**和**即時輸出**，可設定內嵌和封存即時串流內容。
* **資產**，可在 Azure 儲存體中儲存及發佈媒體內容。 
* **串流端點**，可設定和擴展動態封裝、加密和串流處理即時與點播媒體內容。

### <a name="known-issues"></a>已知問題

* 提交工作時，您可以使用 HTTPS URL、SAS URL 或位於 Azure Blob 儲存體中檔案的路徑來指定內嵌您的來源影片。 目前，AMS v3 不支援透過 HTTPS URL 的區塊傳送編碼。

## <a name="provide-feedback"></a>提供意見反應

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[概觀](media-services-overview.md)
