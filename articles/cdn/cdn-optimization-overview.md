---
title: 最佳化您案例的 Azure 內容傳遞
description: 如何最佳化特定案例的內容傳遞
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: rli
ms.openlocfilehash: de748f7fa33b0250b1572dd5ae55cddf15003a0e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765669"
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>最佳化您案例的 Azure 內容傳遞

當您將內容傳遞給所有對象時，請務必確保最佳的傳遞內容。 Azure 內容傳遞網路 (CDN) 可以根據您的內容類型，最佳化傳遞體驗。 內容可以是網站、即時串流、影片或供下載的大型檔案。 當您建立 CDN 端點時，您會在 [最佳化對象] 選項中指定案例。 您的選擇會決定從 CDN 端點傳送的內容套用哪一個最佳化。

最佳化選項旨在使用最佳作法行為改善內容傳遞效能以及更好的原始卸載。 您案例的選擇會修改部分快取、物件區塊化和原始失敗重試原則的組態，進而影響效能。 

本文提供各種最佳化功能概觀和使用時機。 如需功能和限制的詳細資訊，請參閱每個個別最佳化類型的各自文件。

> [!NOTE]
> 您的 [最佳化對象] 選項會隨著您選取的提供者而異。 CDN 提供者會以不同的方式套用增強功能，視案例而定。 

## <a name="provider-options"></a>提供者選項

**來自 Microsoft 的標準 Azure CDN** 支援下列最佳化：

* [一般 Web 傳遞](#general-web-delivery)。 此最佳化也可用於媒體串流處理和大型檔案下載。


**來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN** 支援下列最佳化：

* [一般 Web 傳遞](#general-web-delivery)。 此最佳化也可用於媒體串流處理和大型檔案下載。

* [動態網站加速](#dynamic-site-acceleration) 


**來自 Akamai 的標準 Azure CDN** 支援下列最佳化：

* [一般 Web 傳遞](#general-web-delivery) 

* [一般媒體串流處理](#general-media-streaming)

* [點播視訊媒體串流處理](#video-on-demand-media-streaming)

* [大型檔案下載](#large-file-download)

* [動態網站加速](#dynamic-site-acceleration) 

Microsoft 建議您測試不同提供者之間的效能變化，以選取最佳的傳遞提供者。

## <a name="select-and-configure-optimization-types"></a>選取並設定最佳化類型

若要建立新的端點，請選取最符合案例的最佳化類型，以及您想要端點傳送的內容類型。 **一般 Web 傳遞**是預設選項。 對於現有的**來自 Akamai 的 Azure 內容傳遞網路**端點，您可以隨時更新最佳化選項。 這項變更不會干擾來自 CDN 的傳遞。 

1. 在**來自 Akamai 的標準 Azure CDN** 設定檔內，選取某個端點。

    ![端點選取範圍 ](./media/cdn-optimization-overview/01_Akamai.png)

2. 在 [設定] 下選取 [最佳化]。 然後，從 [最佳化對象] 下拉式清單選取類型。

    ![選取最佳化和類型](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>特定案例最佳化

您可以將下列案例之一的 CDN 端點最佳化。 

### <a name="general-web-delivery"></a>一般 Web 傳遞

一般 Web 傳遞是最常見的最佳化選項。 它是專為一般 Web 內容最佳化所設計，例如網頁和 Web 應用程式。 此最佳化也可用於檔案和視訊下載。

一般網站包含靜態和動態的內容。 靜態內容包括可供不同使用者快取和傳遞的影像、JavaScript 程式庫和樣式表。 動態內容則是個別使用者的個人化內容，例如針對使用者設定檔打造的新聞項目。 動態內容不會被快取，因為它是每位使用者的唯一內容，例如購物車內容。 一般 Web 傳遞可以最佳化整個網站。 

> [!NOTE]
> 如果您使用**來自 Akamai 的標準 Azure CDN**，當平均檔案大小小於 10 MB 時，建議您使用此最佳化。 如果您的平均檔案大小大於 10 MB，請從 [最佳化對象] 下拉式清單選取 [下載大型檔案]。

### <a name="general-media-streaming"></a>一般媒體串流處理

如果您需要在即時串流和點播視訊串流處理使用端點，建議您使用一般媒體串流處理最佳化。

媒體串流處理是有時效性的，因為封包晚抵達用戶端會造成檢視體驗降級，例如影片內容經常要緩衝處理。 媒體串流處理最佳化可降低媒體內容傳遞的延遲，為使用者提供 Smooth Streaming 體驗。 

Azure 媒體服務的客戶經常發生此狀況。 當您使用 Azure 媒體服務時，您會取得一個串流端點，可用於即時和隨選資料流處理。 有了這個案例，客戶就不需要在從即時變更為隨選資料流處理時，切換到另一個端點。 一般媒體串流處理最佳化支援這種情況。

如**來自 Microsoft 的標準 Azure CDN****來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN**，使用一般 Web 傳遞最佳化類型，以提供一般串流媒體內容。

如需媒體串流處理最佳化的詳細資訊，請參閱[媒體串流處理最佳化](cdn-media-streaming-optimization.md)。

### <a name="video-on-demand-media-streaming"></a>點播視訊媒體串流處理

點播視訊媒體串流處理最佳化會改善點播視訊串流處理內容。 如果您在點播視訊串流處理使用端點，您可能想要使用此選項。

如**來自 Microsoft 的標準 Azure CDN****來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN**，使用一般 Web 傳遞最佳化類型，以提供點播視訊串流媒體內容。

如需媒體串流處理最佳化的詳細資訊，請參閱[媒體串流處理最佳化](cdn-media-streaming-optimization.md)。

> [!NOTE]
> 如果 CDN 端點主要是提供點播視訊內容，請使用此最佳化類型。 此最佳化與一般媒體串流處理最佳化的主要差異是連線重試逾時。逾時遠短於使用即時串流案例。

### <a name="large-file-download"></a>大型檔案下載

若為**來自 Akamai 的標準 Azure CDN**，最佳化的大型檔案下載適合超過 10 MB 的內容。 如果您的平均檔案大小小於 10 MB，請使用一般 Web 傳遞。 如果您的平均檔案大小一直都大於 10 MB，為大型檔案分別建立端點可能更有效率。 例如，韌體或軟體更新通常是大型檔案。 若要傳遞大於 1.8 GB 的檔案，則需要大型檔案下載最佳化。

如**來自 Microsoft 的標準 Azure CDN****來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN**，使用一般 Web 傳遞最佳化類型，以提供大型檔案下載內容。 檔案下載大小沒有限制。

如需大型檔案最佳化的詳細資訊，請參閱[大型檔案最佳化](cdn-large-file-optimization.md)。

### <a name="dynamic-site-acceleration"></a>動態網站加速

 動態網站加速**來自 Akamai 的標準 Azure CDN**、**來自 Verizon 的標準 Azure CDN**和**來自 Verizon 的進階 Azure CDN** 設定檔。 使用此最佳化需要額外付費；如需詳細資訊，請參閱[內容傳遞網路定價](https://azure.microsoft.com/pricing/details/cdn/)。

動態網站加速包括有益於動態內容延遲和效能的各種技術。 相關技術包括路由和網路最佳化、TCP 最佳化等等。 

您可以使用此最佳化加速 Web 應用程式，它包含許多無法快取的回應。 範例包括搜尋結果、簽出交易或即時資料。 您可以繼續使用靜態資料的核心 CDN 快取功能。 

如需動態網站加速的詳細資訊，請參閱[動態網站加速](cdn-dynamic-site-acceleration.md)。



