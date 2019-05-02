---
title: Azure 媒體服務封裝和原始錯誤 |Microsoft Docs
description: 本主題說明您可能會收到來自 Azure 媒體服務封裝服務的錯誤。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2019
ms.author: juliako
ms.openlocfilehash: c350a989f23eb667923a53f6eb06dd55905b0fab
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927308"
---
# <a name="media-services-packaging-errors"></a>媒體服務封裝錯誤 

本主題說明您可能會收到來自 Azure 媒體服務的錯誤[封裝服務](streaming-endpoint-concept.md)。

## <a name="400-bad-request"></a>400 不正確的要求

要求包含無效的資訊，並會拒絕這些錯誤碼，因為下列原因之一：

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL 語法或格式錯誤。 範例包括無效的類型、 無效的片段或不正確的追蹤記錄的要求。 |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|要求 URL 中有沒有加密標記。 CMAF 要求需要在 URL 中的加密標籤。 使用一個以上的加密類型設定其他通訊協定也需要去除混淆加密標記。 |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|儲存體來完成要求的要求失敗，不正確的要求時發生錯誤。 |

## <a name="403-forbidden"></a>403 禁止

基於下列原因之一不允許此要求：

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|儲存體來完成要求的要求失敗，發生驗證錯誤。 如果已在輪替儲存體金鑰和服務無法同步儲存體金鑰，會發生這項目。 <br/><br/>請連絡 Azure 支援前往[說明 + 支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)在 Azure 入口網站中。|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |儲存體作業發生錯誤，帳戶權限不足，無法存取。 |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |完成要求儲存體的要求失敗，因為儲存體帳戶已停用。 |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |儲存體作業發生錯誤，因為一般的錯誤而失敗的存取。 |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |輸出格式會被封鎖，因為 StreamingPolicy 中的組態。 |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |加密是必要的內容，都需要的輸出格式傳遞原則。 |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |傳遞原則設定中未設定加密。 |

## <a name="404-not-found"></a>404 找不到

作業嘗試在已不存在的資源上執行。 比方說，資源可能已被刪除。

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |找不到要求的追蹤。 |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |找不到要求的資源。 |
|MPE_UNAUTHORIZED |0x80890244 |未經授權的存取。 |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |找不到要求的時間戳記。 |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |找不到要求的動態資訊清單篩選。 |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |要求的片段索引超出有效範圍。 |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |即時的媒體取得 moov 緩衝區找不到項目。 |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |找不到的片段，在特定的追蹤要求的時間。<br/><br/>可能是片段不在儲存體中。 請嘗試不同的圖層可能會有片段的簡報。 |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |找不到要求的資訊清單中的位元速率媒體項目。 <br/><br/>可能是播放程式要求的特定位元速率不是資訊清單中的視訊播放軌。|
|MPE_METADATA_NOT_FOUND |0x80890257 |找不到資訊清單中的特定中繼資料或找不到從儲存體的重訂基底。 |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |儲存體作業時發生錯誤，找不到資源。 |

## <a name="409-conflict"></a>409 衝突

在上，所提供的資源識別碼`PUT`或`POST`作業已由現有的資源。 若要解決此問題，使用另一個資源識別碼。

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |儲存體作業時發生錯誤，衝突錯誤。  |

## <a name="410"></a>410

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|即時資料流，具有篩選 forceEndTimestamp 設為 true、 開始或結束的時間戳記時，在目前的 DVR 時段之外。|

## <a name="412-precondition-failure"></a>412 先決條件失敗

指定的 eTag 可用在伺服器上，也就是，開放式並行存取錯誤的版本不同的作業。 讀取資源的最新版本及更新要求的 eTag 之後，重試要求。

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |要求之的片段尚未就緒。|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|儲存體作業時發生錯誤，前置條件失敗。|

## <a name="415-unsupported-media-type"></a>415 不支援的媒體類型

用戶端傳送的裝載格式的格式不受支援。

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| 已加密的內容不應該套用加密。|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |加密為輸入的格式無效。|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| 傳遞原則類型而言無效。|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |原始的設定無法共用的多個輸出格式。|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|媒體格式或類型不受支援。 例如，媒體服務不支援超過 64 品質層級計數。 FLV 視訊標記，在媒體服務不支援多個可與多個預存程序的視訊畫面格|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| 不支援要求的資產的輸入的格式。 媒體服務支援 Smooth （即時） MP4 (VoD) 和漸進式下載格式。|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|不支援要求的輸出格式。 媒體服務支援 Smooth、 DASH （CSF、 CMAF）、 HLS (v3，v4 CMAF) 和漸進式下載格式。|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|遇到不支援的加密類型。|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|輸出格式所不支援要求的媒體類型。 支援的類型為視訊、 音訊或 「 SUBT 」 子標題。|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|來源資產媒體已編碼的輸出格式與不相容的媒體格式。|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|來源資產是以不相容的輸出格式的視訊格式編碼。 支援 H.264 AVC、 H.265 （HEVC、 hev1 或 hvc1）。|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|來源資產是以不相容的輸出格式的音訊格式編碼。 支援的音訊格式為 AAC、 E-AC3 （DD +）、 Dolby DTS。|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|來源受保護的資產無法轉換成輸出格式。|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|輸出格式所不支援保護格式。|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|不支援保護格式所輸入的格式。|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|可以 AUD.無效視訊 NAL 單元，比方說，第一個日誌中的範例。|
|MPE_INVALID_NALU_SIZE|0x80890260|無效的 NAL 單元大小。|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|無效的 NAL 單元長度值。|
|MPE_FILTER_INVALID|0x80890236|無效的動態資訊清單篩選。|
|MPE_FILTER_VERSION_INVALID|0x80890237|無效或不受支援的篩選器版本。|
|MPE_FILTER_TYPE_INVALID|0x80890238|無效的篩選類型。|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|無效的範圍篩選條件所指定。|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|篩選器指定無效的追蹤屬性。|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|無效的簡報視窗長度已指定篩選條件。|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|無效的輪詢即時篩選條件所指定。|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|在舊版的篩選條件支援只有一個 absTimeInHNS 元素。|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|沒有任何多個資料流完全套用篩選之後。|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|即時撤退超出 DVR 視窗。|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|即時撤退大於簡報視窗。|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|超過十 （10） 最大允許的預設篩選器。|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|合併的要求篩選中不允許多個第一次的視訊品質運算子。|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|第一個品質位元速率屬性的數目必須是一 (1)。|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS 區段持續時間必須小於三分之一的 DVR 視窗和 HLS 已退出。|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|片段持續時間必須小於或等於約為 20 秒，或輸入的品質等級不對齊的時間。|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS 特有的錯誤，找不到 ReservedBox 它應該呈現 DTSSpecficBox 期間剖析 DTS 方塊。|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS 特有的錯誤，位於 DTSSpecficBox DTS 方塊剖析期間的任何通道。|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS 特有的錯誤，DTSSpecficBox 範例類型不相符。|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS 特有的錯誤，多重資產設但 DTSH 範例類型不符。|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS 特有的錯誤，核心資料流大小無效。|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS 特有的錯誤，範例解析度不正確。|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS 特有的錯誤，子資料流延伸模組索引無效。|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS 特有的錯誤，子資料流區塊編號無效。|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS 特有的錯誤，取樣頻率無效。|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS 特有的錯誤，參考時鐘程式碼，在子資料流的副檔名無效。|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS 特有的錯誤，演講者重新對應組的數目無效。|

如需加密文件和範例，請參閱：

- [概念： 內容保護](content-protection-overview.md)
- [概念：內容金鑰的原則](content-key-policy-concept.md)
- [概念：資料流的原則](streaming-policy-concept.md)
- [使用 AES 加密保護的範例：](protect-with-aes128.md)
- [使用 DRM 保護的範例：](protect-with-drm.md)

如需篩選器的指引，請參閱：

- [概念： 動態資訊清單](filters-dynamic-manifest-overview.md)
- [概念： 篩選](filters-concept.md)
- [範例： 使用 REST Api 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [範例： 使用.NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [範例： 使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

如需即時的文章和範例，請參閱：

- [概念： 即時串流概觀](live-streaming-overview.md)
- [概念：即時事件和即時的輸出](live-events-outputs-concept.md)
- [範例： 即時串流的教學課程](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 範圍不是 」

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|儲存體作業時發生錯誤，傳回 http 416 錯誤，無效的範圍。|

## <a name="500-internal-server-error"></a>500 內部伺服器錯誤

在處理要求時，媒體服務遇到錯誤，而導致無法繼續處理。  

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|接收和從轉譯 Winhttp 錯誤碼 ERROR_WINHTTP_TIMEOUT (0x00002ee2)。|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|接收和從轉譯 Winhttp 錯誤碼 ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe)。|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|接收和從轉譯 Winhttp 錯誤碼 ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7)。|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|儲存體作業時發生錯誤，一般 InternalError 的其中一個 HTTP 500 錯誤。|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|儲存體作業時發生錯誤，其中一個 HTTP 500 錯誤的一般 OperationTimedOut。|
|MPE_STORAGE_FAILURE|0x808900F2|儲存體作業時發生錯誤，比 InternalError 或 OperationTimedOut 其他 HTTP 500 錯誤。|

## <a name="503-service-unavailable"></a>503 服務無法使用

伺服器目前無法接收要求。 此錯誤可能是由於對服務的大量要求所造成。 媒體服務節流機制會針對向服務發出過多要求的應用程式限制資源使用量。

> [!NOTE]
> 請檢查錯誤訊息和錯誤代碼字串，以取得您得到 503 錯誤原因的詳細資訊。 此錯誤不一定表示節流。
> 

|錯誤碼|十六進位值 |錯誤說明|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|儲存體作業發生錯誤，會收到 HTTP 伺服器忙碌錯誤 503。|

## <a name="provide-feedback"></a>提供意見反應

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>請參閱

- [編碼錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Azure 媒體服務概念](concepts-overview.md)
- [配額和限制](limits-quotas-constraints.md)

## <a name="next-steps"></a>後續步驟

[範例： 從 ApiException 搭配.NET 存取錯誤碼和訊息](configure-connect-dotnet-howto.md#connect-to-the-net-client)
