---
title: 建立具有 HTTP 的大門，到使用 Azure 入口網站的 HTTPS 重新導向
description: 了解如何使用從 HTTP 重新導向至使用 Azure 入口網站的 HTTPS 的流量建立前端。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 7fabc1e3445d3dbd357700ffde3caeb985cc60c4
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67601964"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>建立具有 HTTP 的大門，到使用 Azure 入口網站的 HTTPS 重新導向

您可以使用 Azure 入口網站來建立[大門](front-door-overview.md)SSL 終止的憑證。 路由規則用來將 HTTP 流量重新導向至 HTTPS。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立與現有的 Web 應用程式資源的大門
> * 新增自訂網域 SSL 憑證 
> * 設定自訂網域上的 HTTPS 重新導向

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>建立與現有的 Web 應用程式資源的大門

1. 在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。
2. 按一下 Azure 入口網站左上角的 [建立資源]  。
3. 搜尋**大門**使用搜尋列，一旦您找到的資源類型時，按一下 **建立**。
4. 選擇訂用帳戶然後使用現有的資源群組，或建立新的帳戶。 請注意，要求您在 UI 中的位置是資源群組。 您的前端設定將取得部署的所有[Azure 前端的 POP 位置](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service)。

    ![為新的前端設定基本概念](./media/front-door-url-redirect/front-door-create-basics.png)

5. 按一下 [**下一步]** 輸入 [設定] 索引標籤。前端的設定會在三個步驟-新增預設前端主機新增後端集區中的後端，然後建立路由規則，以對應前端主機路由的行為。 

     ![前端組態設計工具](./media/front-door-url-redirect/front-door-designer.png)

6. 按一下 ' **+** ' 上的圖示_前端主機_若要建立前端主機，請輸入您的前端的全域唯一的名稱，為您的預設前端主機 (`\<**name**\>.azurefd.net`)。 按一下 **新增**才能繼續進行下一個步驟。

     ![新增前端主機](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 按一下 ' **+** ' 上的圖示_後端集區_建立後端集區。 提供後端集區的名稱，然後按 '**新增後端**'。
8. 選取後端主機類型為_App service_。 選取您的 web 應用程式所在的訂用帳戶，然後再從下拉式清單中選取特定的 web 應用程式**後端主機名稱**。
9. 按一下 **新增**以儲存後端，並按一下 **新增**儲存後端集區設定。 ![在 後端集區中新增後端](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 按一下 ' **+** ' 上的圖示_路由規則_建立路由。 提供的名稱，用於在說出 'HttpToHttpsRedirect'，並將_接受的通訊協定_欄位設為 **'僅限 HTTP'** 。 請確認適當_前端主機_已選取。  
11. 在上_路線的詳細資訊_區段中，將_路由類型_來**重新導向**，確定_重新導向類型_設為**找到 (302)** 並_重新導向通訊協定_設定為**僅限 HTTPS**。 
12. 按一下 新增儲存 HTTP 至 HTTPS 重新導向的路由規則。
     ![新增 HTTP 至 HTTPS 重新導向路由](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. 新增另一個路由規則，來處理 HTTPS 流量。 按一下 ' **+** ' 登入_路由規則_提供的名稱，用於在說出 'DefaultForwardingRoute'，並將_接受的通訊協定_欄位若要 **'僅限 HTTPS'** 。 請確認適當_前端主機_已選取。
14. 路由詳細資料 區段中，設定_路由類型_要**向前**，確定已選取正確的後端集區和_轉送的通訊協定_設為**僅限 HTTPS**。 
15. 按一下 [新增] 以儲存路由的要求轉送規則。
     ![新增正向的 HTTPS 流量路由](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 按一下 **檢閱 + 建立**，然後**建立**，以建立您的前端設定檔。 請移至 一次建立的資源。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>將自訂網域新增至您的前端，並在其上啟用 HTTPS
下列步驟會展示如何在現有的 「 大門 」 資源上新增自訂網域，並再啟用 HTTP 至 HTTPS 重新導向，在其上。 

### <a name="add-a-custom-domain"></a>新增自訂網域

在此範例中，您將新增的 CNAME 記錄`www`子網域 (例如`www.contosonews.com`)。

#### <a name="create-the-cname-record"></a>建立 CNAME 記錄

新增 CNAME 記錄以將子網域對應至前端的預設前端主機 (`<name>.azurefd.net`，其中`<name>`是您的前端設定檔的名稱)。

針對`www.contoso.com`網域，例如，新增 CNAME 記錄，將名稱對應`www`至`<name>.azurefd.net`。

新增 CNAME 之後，DNS 記錄分頁看起來如下列範例所示：

![CNAME 的自訂網域，大門](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>上架上您的前端的自訂網域

1. 前端設計工具 索引標籤上，按一下 ' +' 區段來新增新的自訂網域的前端主機上的圖示。 
2. 在 [自訂主機名稱] 欄位中，輸入完整的自訂 DNS 名稱範例`www.contosonews.com`。 
3. 一旦已驗證網域的 CNAME 對應到您的前端時，按一下**新增**新增自訂網域。
4. 按一下 **儲存**將變更送出。

![[自訂網域] 功能表](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>您的自訂網域上啟用 HTTPS

1. 按一下 新增自訂網域上和下一節**自訂網域 HTTPS**，將狀態變更為**已啟用**。
2. 您可以將保留**憑證管理類型**設為_受管理的大門_免費憑證維護、 管理、 及 autorotated 的大門。 您也可以選擇使用您自己與 Azure Key Vault 中儲存的自訂 SSL 憑證。 本教學課程會假設前門使用管理憑證。
![啟用自訂網域 HTTPS](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 按一下 **更新**以儲存選取項目，然後按一下**儲存**。
4. 按一下 **重新整理**之後數分鐘，然後按一下 自訂網域，請參閱 < 憑證佈建的進度。 

> [!WARNING]
> 啟用 HTTPS 的自訂網域可能需要幾分鐘，也取決於網域擁有權驗證若 CNAME 不直接對應到您的大門主機`<name>.azurefd.net`。 深入了解[如何啟用自訂網域 HTTPS](./front-door-custom-domain-https.md)。

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>設定自訂網域的路由規則

1. 按一下稍早建立的重新導向路由規則。
2. 前端主機的下拉式清單中按一下，然後選取您要套用此路由，以及您網域的自訂網域。
3. 按一下 [更新]  。
4. 也就是執行相同作業的其他路由規則以及轉送路線新增自訂網域。
5. 按一下 **儲存**提交您的變更。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
- 深入了解[URL 重新導向在前門](front-door-url-redirect.md)。
