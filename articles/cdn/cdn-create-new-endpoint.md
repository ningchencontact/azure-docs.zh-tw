---
title: "開始使用 Azure CDN | Microsoft Docs"
description: "本主題說明如何啟用 Azure 內容傳遞網路 (CDN)。 本教學課程會逐步建立新的 CDN 設定檔和端點。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: f1681b0796885cd6dc880303485edb97c15cda71
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="getting-started-with-azure-cdn"></a>開始使用 Azure CDN
本文說明如何藉由建立新的 CDN 設定檔和端點來啟用 Azure [內容傳遞網路 (CDN)](cdn-overview.md)。

## <a name="create-a-new-cdn-profile"></a>建立新的 CDN 設定檔
CDN 設定檔為 CDN 端點的集合。 每個設定檔皆可包含一或多個 CDN 端點。 若要依網際網路網域、Web 應用程式或其他準則來組織您的 CDN 端點，您可以使用多個設定檔。

> [!NOTE]
> Azure 訂用帳戶對下列資源具有預設限制：
> - 可建立的 CDN 設定檔數目
> - 可在 CDN 設定檔中建立的端點數目 
> - 可對應至端點的自訂網域數目
>
> 如需 CDN 訂用帳戶限制的相關資訊，請參閱 [CDN 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits)。
>
> CDN 定價是根據 CDN 設定檔層級來套用的。 因此，若要混合使用 Azure CDN 定價層，您必須建立多個 CDN 設定檔。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>建立新的 CDN 端點
**建立新的 CDN 端點**

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的 CDN 設定檔。 您可能已在先前步驟中將其釘選至儀表板。 如果沒有，您可藉由選取 [所有服務]，然後選取 [CDN 設定檔] 來尋找。 在 [CDN 設定檔] 窗格中，選取您打算新增至端點的設定檔。 
   
    此時會顯示 [CDN 設定檔] 窗格。
   
    ![CDN 設定檔][cdn-profile-settings]

2. 選取 [端點]。
   
    ![[加入端點] 按鈕][cdn-new-endpoint-button]
   
    此時會顯示 [新增端點] 窗格。
   
    ![新增端點窗格][cdn-add-endpoint]

3. 針對 [名稱]，輸入新 CDN 端點的唯一名稱。 此名稱用於存取位於網域 `<endpointname>.azureedge.net` 的快取資源。

4. 針對 [原始類型]，選取一種原始類型。 針對 Azure 儲存體帳戶選取 [儲存體]、針對 Azure 雲端服務選取 [雲端服務]、針對 Azure Web 應用程式選取 [Web 應用程式]，若為其他任何可公開存取的 Web 伺服器來源 (裝載於 Azure 或其他位置)，則請選取 [自訂原始來源]。
   
    ![CDN 原始來源類型](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. 針對 [原始主機名稱]，選取或輸入您的原始網域。 下拉式清單會列出您在步驟 4 中指定之類型的所有可用原始來源。 如果您選取 [自訂原始來源] 作為您的原始類型，請輸入自訂原始來源的網域。
    
6. 針對 [原始路徑]，輸入您要快取的資源路徑，或保留空白以允許快取您在步驟 5 中指定之網域中的任何資源。
    
7. 針對 [原始主機標頭]，輸入您要 Azure CDN 連同每個要求一起傳送的主機標頭，或保留預設值。
   
   > [!WARNING]
   > 某些類型的原始來源 (例如 Azure 儲存體和 Web Apps) 要求主機標頭必須符合原始來源的網域。 除非您的原始來源需要與其網域不同的主機標頭，否則您應該保留預設值。
   > 
    
8. 在 [通訊協定] 和 [原始連接埠] 中，指定用來存取原始來源之資源的通訊協定和連接埠。 至少必須選取一個通訊協定 (HTTP 或 HTTPS)。 使用 CDN 提供的網域 (`<endpointname>.azureedge.net`) 來存取 HTTPS 內容。 
   
   > [!NOTE]
   > [原始連接埠] 值只會決定端點用來從原始來源擷取資訊的連接埠。 不論 [原始連接埠] 的值為何，端點本身只會透過預設 HTTP 和 HTTPS 連接埠 (80 和 443) 提供給終端用戶端使用。  
   > 
   > 在**來自 Akamai 的 Azure CDN** 設定檔中，端點不允許原始連接埠的完整 TCP 連接埠範圍。 如需不允許的原始連接埠清單，請參閱 [來自 Akamai 的 Azure CDN 允許的原始連接埠](https://msdn.microsoft.com/library/mt757337.aspx)。  
   > 
   > 當您使用 HTTPS 存取 CDN 內容時，有下列限制：
   > 
   > * 使用 CDN 所提供的 SSL 憑證。 不支援第三方憑證。
   > * Azure CDN 自訂網域的 HTTPS 支援僅適用於 **Verizon 提供的 Azure CDN** 產品 (標準版和進階版)。 此功能不支援**透過 Akamai 提供的 Azure CDN** 產品。 如需詳細資訊，請參閱[在 Azure CDN 自訂網域上設定 HTTPS](cdn-custom-ssl.md)。
    
9. 選取 [新增] 來建立新的端點。
   
   端點建立之後，即會出現在設定檔的端點清單中。
    
   ![CDN 端點][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > 因為需要一段時間才能傳播註冊，所以端點不會立即可供使用。 若為 **來自 Akamai 的 Azure CDN** 設定檔，通常會在一分鐘之內完成傳播。 若為**來自 Verizon 的 Azure CDN** 設定檔，則通常會在 90 分鐘之內完成傳播，但在某些情況下可能會更久。
    > 
    > 如果您嘗試在端點組態傳播到 POP 之前，就使用 CDN 網域名稱，您可能會收到 HTTP 404 回應狀態。 如果在端點建立好後已過了好幾個小時，而您仍然收到 404 回應狀態，請參閱[針對傳回 404 狀態的 CDN 端點進行疑難排解](cdn-troubleshoot-endpoint.md)。
    > 
    > 

## <a name="see-also"></a>另請參閱
* [使用查詢字串控制 CDN 要求的快取行為](cdn-query-string.md)
* [如何將 CDN 內容對應至自訂網域](cdn-map-content-to-custom-domain.md)
* [在 Azure CDN 端點上預先載入資產](cdn-preload-endpoint.md)
* [清除 Azure CDN 端點](cdn-purge-endpoint.md)
* [針對傳回 404 狀態的 CDN 端點進行疑難排解](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
