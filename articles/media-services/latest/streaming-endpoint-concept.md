---
title: Azure 媒體服務中的串流端點 (原點) |Microsoft Docs
description: 在 Azure 媒體服務中, 串流端點 (原點) 代表可將內容直接傳遞至用戶端播放機應用程式的動態封裝和串流服務, 或用於進一步散發的內容傳遞網路 (CDN)。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd1dc7b55060e8262b300022f5ffd1b4da5f7922
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350357"
---
# <a name="streaming-endpoints"></a>資料流端點 

在 Microsoft Azure 媒體服務中,[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)代表動態 (即時) 封裝和原始服務, 可以使用其中一個通用串流媒體, 將您的即時和隨選內容直接傳遞至用戶端播放機應用程式通訊協定 (HLS 或破折號)。 此外,**串流端點**也提供動態 (即時) 的加密給領先業界的 drm。

當您建立媒體服務帳戶時，系統會為您建立**預設**串流端點 (處於已停止狀態)。 您無法刪除**預設**串流端點。 您可以在帳戶下建立額外的串流端點 (請參閱[配額和限制](limits-quotas-constraints.md))。 

> [!NOTE]
> 若要開始串流處理視訊，您必須啟動要作為視訊串流處理來源的**串流端點**。 
>  
> 只有當您的串流端點處於 [執行中] 狀態時, 才會向您收取費用。

## <a name="naming-convention"></a>命名慣例

串流 URL 的主機名稱格式為： `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`，其中 `servicename` = 串流端點名稱或即時事件名稱。 

使用預設串流端點時，會省略 `servicename`，因此 URL 為： `{accountname}-{regionname}.streaming.azure.net`。 

### <a name="limitations"></a>限制

* 串流端點名稱的最大值為24個字元。
* 此名稱應遵循此[RegEx](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)模式： `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`。

## <a name="types"></a>型別  

**串流端點**有兩種類型：**標準**(預覽) 和**Premium**。 類型會根據您為串流端點配置的縮放單位數 (`scaleUnits`) 來定義。 

類型描述如下表所示：  

|Type|縮放單位|描述|
|--------|--------|--------|  
|**標準**|0|預設的串流端點是**標準**類型, 可以藉由調整`scaleUnits`來變更為 Premium 類型。|
|**高級**|>0|**Premium**串流端點適用于先進的工作負載, 提供專用且可調整的頻寬容量。 您可以藉由調整`scaleUnits` (串流單位) 移至 Premium 類型。 `scaleUnits` 提供您專用的輸出容量，您可以透過每次增量 200 Mbps 的方式來購買。 使用**進階**類型時，每個啟用的單位會提供額外頻寬容量給應用程式。 |

> [!NOTE]
> 對於想要將內容傳遞給大型網際網路物件的客戶, 我們建議您在串流端點上啟用 CDN。

如需 SLA 資訊, 請參閱[定價和 SLA](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="comparing-streaming-types"></a>比較串流類型

功能|標準|進階
---|---|---
輸送量 |在使用 CDN 時, 最高可達 600 Mbps, 並可提供更高的有效輸送量。|每個串流單位 (SU) 200 Mbps。 使用 CDN 時, 可以提供更高的有效輸送量。
CDN|Azure CDN、協力廠商 CDN 或沒有 CDN。|Azure CDN、協力廠商 CDN 或沒有 CDN。
按比例計費| 每日|每日
動態加密|是|是
動態封裝|是|是
調整規模|自動相應增加至目標輸送量。|其他 su
IP 篩選/G20/自訂主機<sup>1</sup>|是|是
漸進式下載|是|是
建議用法 |建議用於絕大多數的串流案例。|專業用法。

<sup>1</sup>只有在未在端點上啟用 CDN 時, 才直接用於串流端點。<br/>

## <a name="properties"></a>屬性 

本節提供一些串流端點屬性的詳細資料。 如需如何建立新串流端點的範例及所有屬性的說明，請參閱[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)。 

- `accessControl`-用來設定此串流端點的下列安全性設定:Akamai 簽章標頭驗證金鑰和可以連線至此端點的 IP 位址。<br />只有當設定為 false 時`cdnEnabled` , 才能設定這個屬性。
- `cdnEnabled`-指出是否已啟用此串流端點的 Azure CDN 整合 (預設為停用)。 如果您將 `cdnEnabled` 設為 True，下列設定就會停用：`customHostNames` 和 `accessControl`。
  
    並非所有資料中心都支援 Azure CDN 整合。 若要檢查您的資料中心是否有可用的 Azure CDN 整合, 請執行下列動作:
 
  - 嘗試將設定`cdnEnabled`為 true。
  - 檢查傳回的`HTTP Error Code 412` (PreconditionFailed) 結果, 訊息為「串流端點 CdnEnabled 屬性無法設定為 true, 因為目前的區域中無法使用 CDN 功能」。 

    如果您收到此錯誤，表示資料中心不支援此功能。 您應該嘗試使用另一個資料中心。
- `cdnProfile`-當`cdnEnabled`設定為 true 時, 您也可以傳遞`cdnProfile`值。 `cdnProfile` 是 CDN 設定檔的名稱，而該設定檔就是要建立 CDN 端點的位置。 您可以提供現有的 cdnProfile，或使用新的。 如果值為 NULL，而且 `cdnEnabled` 是 True，則會使用預設值 "AzureMediaStreamingPlatformCdnProfile"。 如果提供的 `cdnProfile` 已經存在，則會在其下方建立端點。 如果設定檔不存在, 則會自動建立新的設定檔。
- `cdnProvider`-啟用 CDN 時, 您也可以傳遞`cdnProvider`值。 `cdnProvider` 會控制將使用哪一個提供者。 目前支援三個值："StandardVerizon"、"PremiumVerizon" 和 "StandardAkamai"。 如果未提供任何值, `cdnEnabled`且為 true, 則會使用 "StandardVerizon" (這是預設值)。
- `crossSiteAccessPolicies`-用來指定不同用戶端的跨網站存取原則。 如需詳細資訊，請參閱[跨網域原則檔案規格](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)和[使服務可跨網域界限使用](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)。<br/>這些設定僅適用于 Smooth Streaming。
- `customHostNames`-用來設定串流端點, 以接受導向至自訂主機名稱的流量。  此屬性適用于 Standard 和 Premium 串流端點, 而且可以在下列情況`cdnEnabled`設定: false。
    
    功能變數名稱的擁有權必須由媒體服務確認。 媒體服務藉由要求`CName`記錄包含媒體服務帳戶識別碼, 做為要新增至使用中網域的元件, 來驗證功能變數名稱擁有權。 例如，若要將 "sports.contoso.com" 作為串流端點的自訂主機名稱，`<accountId>.contoso.com` 的記錄就必須設定為指向其中一個媒體服務驗證名稱。 驗證主機名稱會由 verifydns.\<mediaservices-dns-zone> 組成。 

    以下是要在不同 Azure 區域的驗證記錄中使用的預期 DNS 區域。
  
  - 北美洲、歐洲、新加坡、香港特別行政區、日本:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - 中國：
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    例如, 將`CName` "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" 對應至 "verifydns.media.azure.net" 的記錄證明媒體服務識別碼945a4c4e-28ea-45cd-8ccb-a519f6b700ad 具有 contoso.com 網域的擁有權, 因此在 contoso.com 下啟用任何名稱, 以作為該帳戶下串流端點的自訂主機名稱。 若要尋找媒體服務識別碼的值，請前往 [Azure 入口網站](https://portal.azure.com/)，然後選取您的媒體服務帳戶。 **帳戶識別碼**會出現在頁面的右上方。
        
    如果有人嘗試設定自訂主機名稱，但沒有適當的 `CName` 記錄驗證，則 DNS 回應會失敗，然後快取會執行一段時間。 當適當的記錄就緒後，可能需要等待一段時間，才會重新驗證快取的回應。 視自訂網域的 DNS 提供者而定，重新驗證記錄可能會花費幾分鐘到一小時的時間。
        
    除了`CName`對應`<accountId>.<parent domain>`至`CName` `sports.contoso.com`的, 您必須建立另一個將自訂主機名稱 (例如,) 對應到您媒體服務串流端點的主機名稱 (例如, `verifydns.<mediaservices-dns-zone>` `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > 位於相同資料中心的串流端點不能共用相同的自訂主機名稱。

    目前, 媒體服務不支援使用自訂網域的 SSL。 
    
- `maxCacheAge`-覆寫媒體片段和隨選資訊清單上的串流端點所設定的預設最大壽命 HTTP 快取控制標頭。 此值的設定會以秒為單位。
- `resourceState` -

    - 已停止-建立後的串流端點初始狀態
    - 正在開始-正在轉換為執行中狀態
    - 執行-能夠將內容串流至用戶端
    - 調整-縮放單位正在增加或減少
    - 正在停止-正在轉換為已停止狀態
    - 刪除中-正在刪除
    
- `scaleUnits`-提供您專用的輸出容量, 並以 200 Mbps 為單位來購買。 如果您需要移至**進階**類型，請調整 `scaleUnits`。

## <a name="working-with-cdn"></a>使用 CDN

您應該在大部分情況下啟用 CDN。 不過，如果您預期同時觀看人數最多不會超過 500 人，則建議您停用 CDN，因為 CDN 的功能是依據同時觀看人數進行最佳調整。

### <a name="considerations"></a>考量

* 無論您是否啟用 CDN，串流端點 `hostname` 和串流 URL 都保持不變。
* 如果您需要能夠在不使用 CDN 的情況下測試內容, 您可以建立另一個未啟用 CDN 的串流端點。

### <a name="detailed-explanation-of-how-caching-works"></a>詳細說明快取的運作方式

新增 CDN 時並沒有特定的頻寬值，因為在已啟用 CDN 的串流端點上，所需的頻寬量會有所不同。 這大部分取決於內容類型、熱門程度、位元速率及通訊協定。 CDN 只會快取要求的內容。 這表示，熱門內容會直接從 CDN 提供 (前提是已快取視訊片段)。 快取實況內容的機率很高，因為通常會有許多人觀看一模一樣的內容。 隨選內容可能比較難處理，因為可能會有一些內容很熱門，有一些內容較不受歡迎。 如果您有數百萬個不是很熱門的視訊資產 (每週只有 1 個或 2 個觀看者)，但觀看這些不同視訊的人有數千位，則 CDN 會變得較不具效率。 因為此快取遺漏，您會增加串流端點上的負載。
 
您也需要考量彈性資料流的運作方式。 每個個別的視訊片段都會快取為自有的實體。 例如，如果某個影片第一次有人觀看，而此觀看者跳著觀看幾個幾秒鐘的片段，則只有與此觀看者所觀看內容相關聯的視訊片段會快取到 CDN。 因為有彈性資料流，您通常會有 5 到 7 個位元速率不同的視訊。 如果一個人觀看某個位元速率，而另一個人觀看不同的位元速率，則這些位元速率會分別在 CDN 中快取。 即使兩個人觀看相同的位元速率，視訊也可能透過不同的通訊協定來串流處理。 每個通訊協定 (HLS、MPEG DASH、Smooth Streaming) 會分別進行快取。 因此，每一個位元速率和通訊協定都會分別進行快取，而且只會快取已要求的那些視訊片段。

### <a name="enable-azure-cdn-integration"></a>啟用 Azure CDN 整合

使用 CDN 布建串流端點之後, 在完成 DNS 更新以將串流端點對應至 CDN 端點之前, 會在媒體服務定義等待時間。

如果您稍後想要停用/啟用 CDN，串流端點必須處於**已停止**狀態。 可能需要將近 2 小時，Azure CDN 整合才會啟用，變更也才會遍及所有 CDN POP。 不過，您可以啟動串流端點，並從串流端點不停地串流，等到整合完成後，則會從 CDN 傳送資料流。 在布建期間, 您的串流端點會處於**啟動**中狀態, 而您可能會發現效能降低。

建立標準串流端點時, 預設會使用標準 Verizon 來設定它。 您可以使用 REST Api 來設定 Premium Verizon 或 Standard Akamai 提供者。 

中國和聯邦政府區域除外，其他所有 Azure 資料中心都啟用 CDN 整合。

> [!IMPORTANT]
> 如果是標準串流端點，Azure 媒體服務與 Azure CDN 的整合是在**來自 Verizon 的 Azure CDN** 上實作。 您可以使用所有 **Azure CDN 定價層和提供者**來設定進階串流端點。 如需 Azure CDN 功能的詳細資訊，請參閱 [CDN 概觀](../../cdn/cdn-overview.md)。

### <a name="determine-if-dns-change-has-been-made"></a>判斷是否已進行 DNS 變更

您可以使用 https://www.digwebinterface.com, 判斷是否已在串流端點上進行 DNS 變更 (流量會導向至 Azure CDN)。 如果結果中有 azureedge.net 的功能變數名稱, 則流量現在會指向 CDN。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[此存放庫中](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)的範例會示範如何使用 .NET 啟動預設的串流端點。

