---
title: 上架根或頂點的網域，才能使用 Azure 入口網站中現有大門
description: 了解如何上架根或頂點的網域，才能使用 Azure 入口網站中現有大門。
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 464c38b0ece274d2ea7df89ab9fd7c0a60dc4b58
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333010"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>上架根或頂點的網域，在您的大門
Azure 的前端會使用 CNAME 記錄來驗證上架的自訂網域的網域擁有權。 此外，大門不會與您的前端設定檔相關聯的前端 IP 位址上公開，因此您無法將 apex 網域對應至 IP 位址，如果目的是要上架它以 Azure 大門。

DNS 通訊協定可防止在區域頂點指派 CNAME 記錄。 例如，如果您的網域`contoso.com`; 您可以建立的 CNAME 記錄`somelabel.contoso.com`; 但您無法建立 CNAME`contoso.com`本身。 這項限制會發生問題，具有負載平衡的應用程式背後 Azure 前端應用程式擁有者。 因為使用的前端設定檔需要建立的 CNAME 記錄，所以您無法從區域頂點指向大門設定檔。

使用 Azure DNS 別名記錄來解決此問題。 不同於 CNAME 記錄，在區域頂點建立別名記錄，以及應用程式擁有者可以使用它來指向具有公用端點的前端設定檔中的其區域頂點的記錄。 應用程式擁有者指向相同的前端設定檔用於其 DNS 區域內的任何其他網域。 例如，`contoso.com`和`www.contoso.com`可以指向相同的 「 大門 」 設定檔。 

將頂點或根網域對應至您的前端設定檔基本上需要 CNAME 壓平合併或 DNS 搜尋，這是一種機制，在 DNS 中提供者以遞迴方式解析的 CNAME 項目直到達到 IP 位址。 Azure DNS 支援此功能的大門端點。 

> [!NOTE]
> 其他 DNS 提供者也支援壓平合併的 CNAME，或 DNS 搜尋，不過，Azure 大門建議客戶使用 Azure DNS，來裝載其網域。

您可以將頂點網域使用登入 Azure 入口網站，在您的前端，並在其上啟用 HTTPS 的 SSL 終止的憑證與相關聯。 頂點網域也稱為根或裸網域。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立別名記錄指向您的前端設定檔
> * 將根網域新增至大門
> * 安裝程式的根網域的 HTTPS

> [!NOTE]
> 本教學課程需要您已建立的前端設定檔。 像其他教學課程，請參閱[快速入門：建立前端](./quickstart-create-front-door.md)或是[建立 HTTP 至 HTTPS 重新導向的大門](./front-door-how-to-redirect-https.md)開始著手。

## <a name="create-an-alias-record-for-zone-apex"></a>建立區域頂點的別名記錄

1. 開啟**Azure DNS**可以上架網域設定。
2. 建立或編輯區域頂點的記錄。
3. 選取的記錄**型別**做為_A_記錄，然後選取_是_如**別名記錄集**。 **別名類型**應該設定為_Azure 資源_。
4. 選擇 Azure 訂用帳戶，並裝載您的前端設定檔的位置，然後選取 來自的大門資源**Azure 資源**下拉式清單。
5. 按一下 **確定**提交您的變更。

    ![區域頂點的別名記錄](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 上述的步驟會建立指向您的大門資源以及 CNAME 記錄對應 'afdverify' 區域頂點記錄 (範例- `afdverify.contosonews.com`) 到`afdverify.<name>.azurefd.net`這將成為網域登入用於您的前端設定檔。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>上架上您的前端的自訂網域

1. 前端設計工具 索引標籤上，按一下 ' +' 區段來新增新的自訂網域的前端主機上的圖示。
2. 在 [自訂主機名稱] 欄位中，輸入根或頂點的網域名稱範例`contosonews.com`。
3. 一旦已驗證網域的 CNAME 對應到您的前端時，按一下**新增**新增自訂網域。
4. 按一下 **儲存**將變更送出。

![[自訂網域] 功能表](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>您的自訂網域上啟用 HTTPS

1. 按一下 新增自訂網域上和下一節**自訂網域 HTTPS**，將狀態變更為**已啟用**。
2. 選取 **憑證管理類型**要_使用我自己的憑證_。

> [!WARNING]
> 前端的受管理的媒體櫃門憑證管理類型目前不支援頂點或根網域。 唯一可用的啟用大門頂點或根網域上的 HTTPS 的選項使用您自己自訂的 SSL 憑證裝載於 Azure 金鑰保存庫。

3. 請確定您已設定正確的權限的存取您的金鑰保存庫在 UI 中，再繼續進行下一個步驟所述的大門。
4. 選擇**金鑰保存庫帳戶**從您目前訂用帳戶，然後選取適當**祕密**並**祕密版本**對應至正確的憑證。
5. 按一下 **更新**以儲存選取項目，然後按一下**儲存**。
6. 按一下 **重新整理**之後數分鐘，然後按一下 自訂網域，請參閱 < 憑證佈建的進度。 

> [!WARNING]
> 請確定您的 apex 網域建立適當的路由規則，或將網域新增至現有的路由規則。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。