---
title: Azure 的最上層機門服務-URL 重新導向 |Microsoft Docs
description: 這篇文章可協助您了解 Azure 前端服務如何支援 URL 重新導向其路由中，如果設定。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333143"
---
# <a name="url-redirect"></a>URL 重新導向
若要將流量重新導向，您可以使用 Azure 前端服務。 您可以重新導向 （通訊協定、 主機名稱、 路徑、 查詢字串） 的多個層級的流量，所有的功能可設定個別的微服務的重新導向是以路徑為基礎。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。
</br>

![Azure 的前端 URL 重新導向][1]

## <a name="redirection-types"></a>重新導向類型
重新導向類型設定回應狀態碼，以了解重新導向的目的之用戶端。 支援下列類型的重新導向：

- **301 （已永久移動）** :表示目標資源指派給新的永久 URI，而且任何未來的參考，此資源應該使用其中一個括住的 Uri。 使用 http 301 狀態碼，到 HTTPS 重新導向功能。 
- **302 （找到）** :指出目標資源暫時位於不同 URI 之下。 因為在某些情況下可能會改變重新導向，用戶端應該繼續針對未來的要求中使用有效的要求 URI。
- **307 （暫時重新導向）** :表示目標資源暫時位於不同 URI 之下，而且使用者代理程式必須為未變更的要求方法，如果它不會執行自動重新導向至該 URI。 重新導向會隨著時間改變，因為用戶端應該繼續使用原始的有效要求 URI，針對未來的要求。
- **308 （永久重新導向）** :表示目標資源指派給新的永久 URI，而且任何未來的參考，此資源應該使用其中一個括住的 Uri。 連結編輯功能，用戶端應該會自動重新連結參考有效的要求 URI 一或多個新的參考可能由伺服器傳送。

## <a name="redirection-protocol"></a>重新導向的通訊協定
您可以設定可用於重新導向的通訊協定。 這可讓其中一個最常見使用案例的重新導向功能，設定 HTTP 至 HTTPS 重新導向。

- **僅限 HTTPS**:設定通訊協定為 HTTPS，如果您想要將從 HTTP 至 HTTPS 流量重新導向。 Azure 的前端服務建議，您應該一律重新導向至 HTTPS 只設定。
- **僅限 HTTP**:這會將連入要求重新導向至 HTTP。 使用此值，如果您想要保留您的流量 HTTP 是唯一的、 未加密。
- **比對要求**:這個選項會保留傳入的要求所使用的通訊協定。 因此，HTTP 要求會維持 HTTP 和 HTTPS 要求會維持 HTTPS post 的重新導向。

## <a name="destination-host"></a>目的主機
在設定重新導向路由，您也可以變更主機名稱或網域重新導向要求。 您可以設定此欄位變更重新導向 URL 中的主機名稱或否則保留從傳入要求的主機名稱。 因此，使用此欄位可以重新導向所有的要求上傳送 https://www.contoso.com/ * 來 https://www.fabrikam.com/ *。

## <a name="destination-path"></a>目的地路徑
您想要用來取代一部分的重新導向的 URL 的路徑區段的情況下，您可以設定此欄位，以新的路徑值。 否則，您可以選擇重新導向過程中保留的路徑值。 因此，此欄位，您可以使用重新導向所有的要求傳送至 https://www.contoso.com/ * 來 https://www.contoso.com/redirected-site 。

## <a name="query-string-parameters"></a>查詢字串參數
您也可以取代重新導向 URL 中的查詢字串參數。 若要取代任何現有的查詢字串，從內送的要求 URL，請將此欄位 'Replace' 來設定並將適當的值。 否則，您可以藉由設定為 'Preserve' 欄位保留原始的查詢字串的集合。 例如，此欄位中，您可以使用重新導向所有流量傳送至 https://www.contoso.com/foo/bar 至 https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F 。 

## <a name="destination-fragment"></a>目的地片段
目的地片段 '#' 之後，通常用來登入頁面上的特定區段的 瀏覽器是 URL 的部分。 您可以設定此欄位將片段新增至重新導向 URL。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png