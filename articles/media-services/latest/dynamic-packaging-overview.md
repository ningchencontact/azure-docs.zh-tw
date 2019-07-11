---
title: Azure 媒體服務動態封裝概觀 | Microsoft Docs
description: 此文章能提供 Azure 媒體服務中動態封裝的概觀。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 4836ec4bb66bbf8ced921dd1095665d004f8a28b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542583"
---
# <a name="dynamic-packaging"></a>動態封裝

Microsoft Azure Media Services 可用來針對數種用戶端技術 (例如 iOS 和 XBOX) 提供許多媒體來源檔案格式、媒體串流格式和內容保護格式。 這些用戶端各自使用不同的通訊協定，例如 iOS 需要 HTTP 即時串流 (HLS) 格式，而 Silverlight 與 Xbox 需要 Smooth Streaming。 如果您有一組自動調整位元速率 (多位元速率) MP4 (ISO Base Media 14496-12) 檔案或一組自動調整位元速率 Smooth Streaming 檔案，且想要將它們傳遞給了解 HLS、MPEG DASH 或 Smooth Streaming 的用戶端，便可以利用「動態封裝」  。 視訊解析度對於封裝而言是無從驗證的，且支援 SD/HD/UHD-4K。

在媒體服務中，[串流端點](streaming-endpoint-concept.md)代表動態 (Just-In-Time) 封裝及原始服務，其可使用其中一種常見的串流媒體通訊協定 (HLS 或 DASH) 來為您提供即時且隨選的內容，並直接傳遞至用戶端播放應用程式。 動態封裝是隨附於所有**串流端點** (標準或進階) 的標準功能。 

若要利用動態封裝，您必須具有內含一組調適性位元速率 MP4 檔案的**資產**，以及媒體服務動態封裝所需的串流設定檔。 取得這些檔案的方法之一，是使用媒體服務編碼您的夾層 (來源) 檔案。 若要讓已編碼資產中的視訊可供用戶端播放，您必須建立**串流定位器**並建置串流 URL。 接著，根據串流用戶端資訊清單中指定的格式 (HLS、DASH 或 Smooth)，您會以您所選擇的通訊協定接收資料流。

因此，您只需要儲存及支付一種儲存格式之檔案的費用，媒體服務會根據用戶端的要求建置及提供適當的回應。 

在媒體服務中，每當您進行即時或隨選的串流時，便會使用動態封裝。 

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

## <a name="on-demand-streaming-workflow"></a>隨選資料流處理工作流程

以下是適用於搭配動態封裝之媒體服務隨選資料流處理的常見工作流程：

1. 上傳輸入或來源檔案 (稱為「夾層」  檔案)。 範例包括 MP4、MOV 或 MXF 檔案。 
1. 將夾層檔編碼為 H.264 MP4 自動調整位元速率集。 
1. 發佈包含自適性位元速率 MP4 集的資產。 您會透過建立串流定位器來發佈。
1. 建置以不同格式 (HLS、MPEG-DASH 及 Smooth Streaming) 為目標的 URL。 串流端點會處理向不同格式提供正確資訊清單和要求的工作。

下圖顯示搭配動態封裝之隨選資料流處理的工作流程：

![搭配動態封裝之隨選資料流處理工作流程的圖表](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="live-streaming-workflow"></a>即時串流工作流程

即時事件可以是下列兩種類型之一：即時通行或即時編碼。 

以下是適用於搭配動態封裝之即時串流的常見工作流程：

1. 建立[即時事件](live-events-outputs-concept.md)。
1. 取得內嵌 URL 並設定您的內部部署編碼器，以使用該 URL 來傳送貢獻摘要。
1. 取得預覽 URL 並使用它來確認已收到來自編碼器的輸入。
1. 建立新的資產。
1. 建立即時輸出並使用您所建立的資產名稱。<br />即時輸出會將資料流封存到資產中。
1. 使用內建的串流原則類型來建立串流定位器。<br />若您想要將內容加密，請檢閱[內容保護概觀](content-protection-overview.md)。
1. 列出串流定位器上的路徑，以取得要使用的 URL。
1. 取得您想要串流之來源串流端點的主機名稱。
1. 建置以不同格式 (HLS、MPEG-DASH 及 Smooth Streaming) 為目標的 URL。 串流端點會處理向不同格式提供正確資訊清單和要求的工作。

下圖顯示搭配動態封裝之即時串流的工作流程：

![搭配動態封裝之即時通行編碼工作流程的圖表](./media/live-streaming/pass-through.svg)

如需媒體服務 v3 中即時串流的相關資訊，請參閱[即時串流概觀](live-streaming-overview.md)。

## <a name="delivery-protocols"></a>傳遞通訊協定

您可以在媒體服務動態封裝中針對您的內容使用這些傳遞通訊協定：

|通訊協定|範例|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="encode-to-adaptive-bitrate-mp4s"></a>編碼為調適性位元速率 MP4

下列文章會示範[如何使用媒體服務來對視訊進行編碼](encoding-concept.md)：

* [使用內建預設從 HTTPS URL 進行編碼](job-input-from-http-how-to.md)
* [使用內建預設對本機檔案進行編碼](job-input-from-local-file-how-to.md)
* [建置自訂預設來以特定案例或裝置需求為目標](customize-encoder-presets-how-to.md)

請參閱媒體編碼器標準[格式和轉碼器](media-encoder-standard-formats.md)的清單。

## <a name="video-codecs"></a>視訊轉碼器

動態封裝支援下列視訊轉碼器：
* MP4 檔案，其包含使用 [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC 或 AVC1) 或 [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC、hev1 或 hvc1) 編碼的視訊。

## <a name="audio-codecs"></a>音訊轉碼器

動態封裝支援下列音訊轉碼器：
* MP4 檔案
* 多重音訊音軌

動態封裝不支援包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音訊的檔案 (它是舊版的轉碼器)。

### <a name="mp4-files"></a>MP4 檔案

動態封裝支援 MP4 檔案，其包含以下列通訊協定編碼的音訊： 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC、HE-AAC v1 或 HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 或 E-AC3)
* Dolby Atmos<br />
   若要串流 Dolby Atmos 內容，可透過搭配 Common Streaming Format (CSF) 或 Common Media Application Format (CMAF) 分散式 MP4 的 MPEG-DASH 通訊協定之類的標準，或是透過搭配 CMAF 的 HTTP 即時串流 (HLS) 來達成。

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   由 DASH-CSF、DASH-CMAF、HLS-M2TS 及 HLS-CMAF 封裝格式所支援的 DTS 轉碼器為：  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution 和 DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (無核心) (dtsl)

### <a name="multiple-audio-tracks"></a>多重音訊音軌

動態封裝針對具有多個音訊音軌及多個轉碼器和語言的串流資產，支援適用於 HLS 輸出 (4 版或更新版本) 的多重音訊音軌。

## <a name="dynamic-encryption"></a>動態加密

您可以使用「動態加密」  來搭配 AES-128 或下列三個主流數位版權管理 (DRM) 系統之一，以動態方式加密您的即時或隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，傳遞 AES 金鑰和 DRM 授權給授權用戶端。 如需詳細資訊，請參閱[動態加密](content-protection-overview.md)。

## <a name="manifest-examples"></a>資訊清單範例 
 
在媒體服務動態封裝中，適用於 HLS、MPEG-DASH 及 Smooth Streaming 的串流用戶端資訊清單會根據 URL 中的格式選取器以動態方式產生。 如需詳細資訊，請參閱[傳遞通訊協定](#delivery-protocols)。 

資訊清單檔案包含串流中繼資料，例如資料軌類型 (音訊、視訊或文字)、資料軌名稱、開始和結束時間、位元速率 (品質)、資料軌語言、簡報視窗 (持續時間固定的滑動視窗)，以及視訊轉碼器 (FourCC)。 此檔案也會透過提供下一個可播放視訊片段及其位置的相關資訊，來指示播放程式擷取下一個片段。 片段 (或區段) 實際上是視訊內容的「區塊」。

### <a name="hls"></a>HLS

以下是 HLS 資訊清單檔的範例，其也被稱為 HLS 主要播放清單： 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="mpeg-dash"></a>MPEG-DASH

以下是 MPEG-DASH 資訊清單檔的範例，其也被稱為 MPEG-DASH 媒體呈現描述 (MPD)：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

以下是 Smooth Streaming 資訊清單檔：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>動態資訊清單

若要控制傳送給播放程式的資料軌數目、格式、位元速率及呈現時間範圍，您可以搭配媒體服務動態封裝工具使用動態篩選。 如需詳細資訊，請參閱[使用動態封裝工具預先篩選資訊清單](filters-dynamic-manifest-overview.md)。

## <a name="more-information"></a>詳細資訊

請參閱 [Azure 媒體服務社群](media-services-community.md)，以了解針對媒體服務詢問問題、提供意見反應，以及取得相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

了解如何[上傳、編碼和串流影片](stream-files-tutorial-with-api.md)。

