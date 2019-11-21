---
title: 在區域頂點裝載負載平衡的 Azure Web 應用程式
description: 使用 Azure DNS 別名記錄，在區域頂點裝載負載平衡的 Web 應用程式
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: allensu
ms.openlocfilehash: a673a74f8f6f919e7ebb7fc3b065ee0742ab3a10
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212358"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>在區域頂點裝載負載平衡的 Azure Web 應用程式

DNS 通訊協定可防止在區域頂點指派 A 或 AAAA 記錄以外的任何項目。 範例區域頂點為 contoso.com。 此限制會為在流量管理員後面有負載平衡應用程式的應用程式擁有者帶來問題。 您無法從區域頂點記錄指向流量管理員設定檔。 因此，應用程式擁有者必須使用因應措施。 位於應用程式層的重新導向，必須從區域頂點重新導向至另一個網域。 An example is a redirect from contoso.com to www\.contoso.com. 此種安排會為重新導向功能帶來單一失敗點。

透過別名記錄，此問題已不存在。 應用程式擁有者現在可以將其區域頂點記錄指向具有外部端點的流量管理員設定檔。 應用程式擁有者可以指向相同的流量管理員設定檔，以用於其 DNS 區域內的任何其他網域。

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. 只要流量管理員設定檔僅設定外部端點，就是這種情況。

在本文中，您將了解如何建立網域頂點的別名記錄，並為 Web 應用程式設定流量管理員設定檔端點。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

必須提供可用的網域名稱，如此才能裝載於 Azure DNS 進行測試。 您必須擁有此網域的完整控制權。 完整控制權包括能夠設定網域的名稱伺服器 (NS) 記錄。

關於在 Azure DNS 中裝載網域的指示，請參閱[教學課程：在 Azure DNS 中裝載您的網域](dns-delegate-domain-azure-dns.md)。

本教學課程使用的範例網域是 contoso.com，但請使用您自己的網域名稱。

## <a name="create-a-resource-group"></a>建立資源群組

建立資源群組來保留本文中用到的所有資源。

## <a name="create-app-service-plans"></a>建立 App Service 方案

使用下表的設定資訊，在您的資源群組中建立兩個 Web App Service 方案。 如需建立 App Service 方案的詳細資訊，請參閱[管理 Azure 中的 App Service 方案](../app-service/app-service-plan-manage.md)。


|Name  |作業系統  |Location  |價格層次  |
|---------|---------|---------|---------|
|ASP-01     |Windows|美國東部|開發/測試 D1-共用|
|ASP-02     |Windows|美國中部|開發/測試 D1-共用|

## <a name="create-app-services"></a>建立應用程式服務

建立兩個 Web 應用程式，每個 App Service 方案中各一個。

1. On upper left corner of the Azure portal page, select **Create a resource**.
2. 在搜尋列中輸入 **Web 應用程式**，然後按 Enter 鍵。
3. Select **Web App**.
4. 選取 [建立]。
5. 接受預設值，並使用下表來設定這兩個 Web 應用程式：

   |Name<br>(在 .azurewebsites.net 內必須是唯一的)|資源群組 |執行階段堆疊|地區|App Service 方案/位置
   |---------|---------|-|-|-------|
   |App-01|使用現有項目<br>選取您的資源群組|.NET Core 2.2|美國東部|ASP-01(D1)|
   |App-02|使用現有項目<br>選取您的資源群組|.NET Core 2.2|美國中部|ASP-02(D1)|

### <a name="gather-some-details"></a>收集一些詳細資料

Now you need to note the IP address and host name for the web apps.

1. Open your resource group and select your first web app (**App-01** in this example).
2. In the left column, select **Properties**.
3. 記下 [URL] 下方的位址，並在 [輸出 IP 位址] 下方，記下清單中的第一個 IP 位址。 You'll use this information later when you configure your Traffic Manager end points.
4. 針對 **App-02** 重複執行。

## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

在您的資源群組中建立流量管理員設定檔。 使用預設值，然後在 trafficmanager.net 命名空間內輸入唯一名稱。

如需建立流量管理員設定檔的相關資訊，請參閱[快速入門：建立流量管理員設定檔以獲得高可用性的 Web 應用程式](../traffic-manager/quickstart-create-traffic-manager-profile.md)。

### <a name="create-endpoints"></a>建立端點

現在您可以針對這兩個 Web 應用程式建立端點。

1. Open your resource group and select your Traffic Manager profile.
2. In the left column, select **Endpoints**.
3. 選取 [新增]。
4. 使用下表來設定端點：

   |Type  |Name  |確定目標  |Location  |自訂標題設定|
   |---------|---------|---------|---------|---------|
   |外部端點     |End-01|您針對 App-01 記錄的 IP 位址|美國東部|host:\<您針對 App-01 記錄的 URL\><br>範例：**host:app-01.azurewebsites.net**|
   |外部端點     |End-02|您針對 App-02 記錄的 IP 位址|美國中部|host:\<您針對 App-02 記錄的 URL\><br>範例：**host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>建立 DNS 區域

您可以使用現有的 DNS 區域進行測試，或者可建立新的區域。 若要在 Azure 中建立及委派新的 DNS 區域，請參閱[教學課程：在 Azure DNS 中裝載您的網域](dns-delegate-domain-azure-dns.md)。

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Add a TXT record for custom domain validation

When you add a custom hostname to your web apps, it will look for a specific TXT record to validate your domain.

1. Open your resource group and select the DNS zone.
2. 選取 [記錄集]。
3. Add the record set using the following table. For the value, use the actual web app URL that you previously recorded:

   |Name  |Type  |Value|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>新增自訂網域

Add a custom domain for both web apps.

1. Open your resource group and select your first web app.
2. In the left column, select **Custom domains**.
3. Under **Custom Domains**, select **Add custom domain**.
4. Under **Custom domain**, type your custom domain name. 例如 contoso.com。
5. 選取 [驗證]。

   Your domain should pass validation and show green check marks next to **Hostname availability** and **Domain ownership**.
5. 選取 [新增自訂網域]。
6. 若要在 [指派至網站的主機名稱] 下方查看新的主機名稱，請重新整理瀏覽器。 The refresh on the page doesn't always show changes right away.
7. 針對第二個 Web 應用程式重複執行此程序。

## <a name="add-the-alias-record-set"></a>新增別名記錄集

Now add an alias record for the zone apex.

1. Open your resource group and select the DNS zone.
2. 選取 [記錄集]。
3. 使用下表來新增記錄集：

   |Name  |Type  |別名記錄集  |別名類型  |Azure 資源|
   |---------|---------|---------|---------|-----|
   |@     |A|是|Azure 資源|流量管理員 - 您的設定檔|


## <a name="test-your-web-apps"></a>測試您的 Web 應用程式

Now you can test to make sure you can reach your web app and that it's being load balanced.

1. 開啟網頁瀏覽器，然後瀏覽至您的網域。 例如 contoso.com。 您應該會看見預設的 Web 應用程式頁面。
2. 停止您的第一個 Web 應用程式。
3. 關閉網頁瀏覽器，並等待數分鐘。
4. 啟動網頁瀏覽器，然後瀏覽至您的網域。 您應該仍會看見預設的 Web 應用程式頁面。
5. 停止您的第二個 Web 應用程式。
6. 關閉網頁瀏覽器，並等待數分鐘。
7. 啟動網頁瀏覽器，然後瀏覽至您的網域。 您應該會看見錯誤 403，指出 Web 應用程式已停止。
8. 啟動您的第二個 Web 應用程式。
9. 關閉網頁瀏覽器，並等待數分鐘。
10. 啟動網頁瀏覽器，然後瀏覽至您的網域。 您應該會再次看見預設的 Web 應用程式頁面。

## <a name="next-steps"></a>後續步驟

若要深入了解別名記錄，請參閱下列文章：

- [教學課程：設定別名記錄來參考 Azure 公用 IP 位址](tutorial-alias-pip.md)
- [教學課程：使用流量管理員來設定別名記錄以支援頂點網域名稱](tutorial-alias-tm.md)
- [DNS 常見問題](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

To learn how to migrate an active DNS name, see [Migrate an active DNS name to Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
