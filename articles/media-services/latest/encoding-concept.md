---
title: 使用媒體服務在雲端編碼 - Azure | Microsoft Docs
description: 本主題說明使用 Azure 媒體服務時的編碼程序
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b0a71e8b3ffff822521a23aafd6764bcce9bd4d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303924"
---
# <a name="encoding-with-media-services"></a>使用媒體服務編碼

媒體服務中的詞彙編碼套用至轉換包含數位視訊和 （或） 到另一個目的是減少檔案的大小，（a） 及/或 （b） 產生與相容的格式從一種標準格式的音訊檔案的程序將範圍廣泛的裝置和應用程式。 此程序也稱為視訊壓縮或轉碼。 請參閱[資料壓縮](https://en.wikipedia.org/wiki/Data_compression)並[編碼及轉碼是什麼？](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)概念的進一步討論。

視訊通常會傳遞至裝置與應用程式[漸進式下載](https://en.wikipedia.org/wiki/Progressive_download)或透過[調適性位元速率串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)。 

* 若要依漸進式下載進行傳遞，您可以使用 Azure 媒體服務將數位媒體檔案 （夾層） 轉換成[MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14)檔案，其中包含與已編碼的視訊[H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC)轉碼器，及使用已編碼的音訊[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)轉碼器。 這個的 MP4 檔案會寫入儲存體帳戶中的資產。 您可以使用 Azure 儲存體 Api 或 Sdk (例如[儲存體 REST API](../../storage/common/storage-rest-api-auth.md)， [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md)，或[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) 直接下載檔案。 如果您已建立輸出資產使用特定的容器名稱，在儲存體，使用該位置。 否則，您可以使用媒體服務[列出資產容器 Url](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。 
* 若要準備的調適性位元速率串流傳遞內容，將夾層檔必須編碼多個位元速率 （高到低）。 若要確保、 非失誤性轉換，如為位元速率降低，因此是品質的影片的解析度。 這會導致所謂的編碼階梯 – 解析度和位元速率的資料表 (請參閱[自動產生的調適性位元速率階梯](autogen-bitrate-ladder.md))。 您可以使用媒體服務編碼夾層檔在多個位元速率 – 在此情況下，您會收到一組 MP4 檔案和相關聯資料流的組態檔，寫入至儲存體帳戶中的資產。 然後您可以使用[動態封裝](dynamic-packaging-overview.md)媒體服務功能的視訊透過串流處理通訊協定，例如傳遞[MPEG DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP)並[HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)。 這需要您建立[串流定位器](streaming-locators-concept.md)並建置串流 Url 對應至支援的通訊協定，可以稍後可遞交給其功能為基礎的裝置/應用程式。

下圖顯示隨編碼使用動態封裝的工作流程。

![動態封裝](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

本主題會指導您如何使用媒體服務 v3 將內容編碼。

## <a name="transforms-and-jobs"></a>轉換和作業

若要使用媒體服務 v3 來編碼，您需要建立[轉換](https://docs.microsoft.com/rest/api/media/transforms) \(英文\) 和[作業](https://docs.microsoft.com/rest/api/media/jobs) \(英文\)。 轉換是定義方法，以在您的編碼設定與輸出;工作是配方的執行個體。 如需詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)

使用媒體服務編碼時，需使用預設來告訴編碼器應該如何處理輸入媒體檔案。 例如，您可以指定所編碼內容中需要的視訊解析度及/或音訊聲道數目。 

您可以使用依據業界最佳做法所建議的其中一個內建預設來快速開始，也可以選擇建置以特定案例或裝置需求為標的的自訂預設。 如需詳細資訊，請參閱[使用自訂轉換進行編碼](customize-encoder-presets-how-to.md)。 

從 2019 年 1 月開始，使用「媒體編碼器標準」來編碼以產生 MP4 檔案時，會產生新的 .mpi 檔案並將該檔案新增到輸出資產。 此 MPI 檔案的目的是用來改進[動態封裝](dynamic-packaging-overview.md)與串流處理案例的效能。

> [!NOTE]
> 您不應該修改或移除該 MPI 檔案，也不應該在您的服務中相依於此類檔案的存在與否。

### <a name="creating-job-input-from-an-https-url"></a>從 HTTPS URL 建立作業輸入

當您提交工作，以便處理您的影片時，您必須告知媒體服務來尋找輸入的視訊的位置。 其中一個選項是指定為作業輸入的 HTTPS URL。 目前，媒體服務 v3 不支援區塊的傳輸編碼透過 HTTPS Url。 

#### <a name="examples"></a>範例

* [從.NET 使用 HTTPS URL 編碼](stream-files-dotnet-quickstart.md)
* [使用 REST 的 HTTPS URL 編碼](stream-files-tutorial-with-rest.md)
* [從使用 CLI 的 HTTPS URL 編碼](stream-files-cli-quickstart.md)
* [從 Node.js 使用 HTTPS URL 編碼](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>從本機檔案建立作業輸入

輸入視訊可以儲存為媒體服務資產，在這種情況下，您會建立一個以檔案為基礎的輸入資產 (儲存在本機或 Azure Blob 儲存體)。 

#### <a name="examples"></a>範例

[將本機檔案，使用內建的預設編碼](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>建立子剪輯作業輸入

進行視訊編碼時，您可以指定也修剪或裁剪的原始程式檔，並產生具有所需的一小部分的輸入視訊的輸出。 這項功能適用於任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)所建置使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)預設值，或有[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)預設值。 

您可以指定要建立[作業](https://docs.microsoft.com/rest/api/media/jobs/create)與單一剪輯的視訊點播或即時封存 （記錄的事件）。 工作輸入可能是資產或 HTTPS URL。

> [!TIP]
> 如果您想要串流您的影片 sublip 沒有 reencoding 影片，請考慮使用[預先篩選與動態封裝程式資訊清單](filters-dynamic-manifest-overview.md)。

#### <a name="examples"></a>範例

請參閱範例：

* [子剪輯影片，以使用.NET](subclip-video-dotnet-howto.md)
* [子剪輯影片，以使用 REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>內建預設

媒體服務目前支援下列內建編碼預設：  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) \(英文\) 是用來設定內建預設，以供使用標準編碼器為輸入視訊編碼時使用。 

目前支援的預設如下：

- **EncoderNamedPreset.AACGoodQualityAudio** - 會產生只包含立體聲音訊 (以 192 kbps 編碼) 的單一 MP4 檔案。
- **EncoderNamedPreset.AdaptiveStreaming** (建議)。 如需詳細資訊，請參閱[自動產生位元速率階梯](autogen-bitrate-ladder.md)。
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -實驗性的預設內容感知的編碼公開 （expose)。 指定任何輸入的內容，服務會嘗試自動判斷最佳的圖層、 適當的位元速率與解析度設定為傳遞數目彈性資料流。 基礎演算法將會繼續隨著時間演進。 輸出會包含交錯的音訊和視訊的 MP4 的檔案。 如需詳細資訊，請參閱 <<c0> [ 感知內容的編碼預設值的實驗性](cae-experimental.md)。
- **EncoderNamedPreset.H264MultipleBitrate1080p** - 會產生一組 8 個對齊 GOP 的 MP4 檔案 (範圍從 6000 kbps 到 400 kbps) 和立體聲 AAC 音訊。 解析度起自 1080p，下至 360p。
- **EncoderNamedPreset.H264MultipleBitrate720p** - 會產生一組 6 個對齊 GOP 的 MP4 檔案 (範圍從 3400 kbps 到 400 kbps) 和立體聲 AAC 音訊。 解析度起自 720p，下至 360p。
- **EncoderNamedPreset.H264MultipleBitrateSD** - 會產生一組 5 個對齊 GOP 的 MP4 檔案 (範圍從 1600 kbps 到 400 kbps) 和立體聲 AAC 音訊。 解析度起自 480p，下至 360p。
- **EncoderNamedPreset.H264SingleBitrate1080p** -產生 MP4 檔案，其中影片以 6750 kbps 和圖片 1080年像素的高度，在 H.264 轉碼器編碼，而是立體聲的音訊以 64 kbps AAC-LC 轉碼器編碼。
- **EncoderNamedPreset.H264SingleBitrate720p** -產生視訊編碼的 H.264 轉碼器 4500 kbps 與圖片高度為 720 像素，而是立體聲的音訊以 64 kbps AAC-LC 轉碼器編碼的 MP4 檔案。
- **EncoderNamedPreset.H264SingleBitrateSD** -產生 MP4 檔案，其中影片以 2200 kbps 與圖片的高度 480 像素為單位的 H.264 轉碼器編碼，而是立體聲的音訊以 64 kbps AAC-LC 轉碼器編碼。

若要查看最新的預設值清單，請參閱[內建的預設設定，以便用來編碼視訊](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。

若要查看如何使用預設值，請參閱[正在上傳、 編碼及串流檔案](stream-files-tutorial-with-api.md)。

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) \(英文\) 能描述在使用標準編碼器為輸入視訊編碼時所要使用的設定。 在自訂轉換預設時，請使用此預設。 

#### <a name="considerations"></a>考量

在建立自訂的預設設定時，適用下列考量：

- 高度和寬度 AVC 內容上的所有值必須都是 4 的倍數。
- Azure 媒體服務 v3 中所有的編碼位元速率是每秒位元。 這是我們使用千位元/秒為單位的 v2 Api 與預設值不同。 比方說，如果 （kb/秒） 指定在 v2 中的位元速率為 128，v3 中它會設 128000 （位元/秒）。

### <a name="customizing-presets"></a>自訂預設值

「媒體服務」可完整支援自訂預設中的所有值，以滿足您的特定編碼需要和需求。 如需示範如何自訂編碼器預設值的範例，請參閱：

#### <a name="examples"></a>範例

- [自訂預設使用.NET](customize-encoder-presets-how-to.md)
- [自訂預設使用 CLI](custom-preset-cli-howto.md)
- [自訂預設使用 REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>預設的結構描述

媒體服務 v3 中預設會是強型別的 API 本身中的實體。 您可以找到這些物件中的 「 結構描述 」 定義[Open API 規格 （或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)。 您也可以檢視的預設的定義 (例如**StandardEncoderPreset**) 中[REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)， [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) （或其他媒體服務 v3 SDK 參考文件）。

## <a name="scaling-encoding-in-v3"></a>在 v3 中調整編碼

若要調整媒體處理，請參閱[使用 CLI 進行調整](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [使用媒體服務上傳、編碼和串流](stream-files-tutorial-with-api.md)
* [從使用內建的預先設定的 HTTPS URL 編碼](job-input-from-http-how-to.md)
* [將本機檔案，使用內建的預設編碼](job-input-from-local-file-how-to.md)
* [建置自訂預設值為目標的特定案例或裝置需求](customize-encoder-presets-how-to.md)