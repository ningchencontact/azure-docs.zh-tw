---
title: 在 Azure Web Apps 中為網域和 SSL 憑證問題疑難排解 | Microsoft Docs
description: 在 Azure Web Apps 中為網域和 SSL 憑證問題疑難排解
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>在 Azure Web Apps 中為網域和 SSL 憑證問題疑難排解

本文列出為 Azure Web Apps 設定網域或 SSL 憑證時，可能會遇到的常見問題。 文中也會描述這些問題的可能原因和解決方案。

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您也可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。

## <a name="certificate-problems"></a>憑證問題

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>無法將繫結 SSL 憑證新增至 Web 應用程式 

### <a name="symptom"></a>徵狀

當您新增 SSL 繫結時，收到下列錯誤訊息：

**無法新增 SSL 繫結。無法為現有 VIP 設定憑證，因為已經有另一個 VIP 使用該憑證。**

### <a name="cause"></a>原因

多個 Web 應用程式之間，如果有多個基於相同 IP 位址的 IP 為主 SSL 繫結，可能會發生此問題。 例如，Web 應用程式 A 的 IP 為主 SSL 使用舊憑證。 Web 應用程式 B 的 IP 為主 SSL 使用同一個 IP 位址的新憑證。 當您更新使用新憑證的 Web 應用程式 SSL 繫結時，由於另一個應用程式也使用相同的 IP 位址，因此更新會失敗，而且會出現這個錯誤。 

### <a name="solution"></a>解決方法 

若要修正問題，請使用下列其中一種方法：

- 刪除使用舊憑證之 Web 應用程式上的 IP 為主 SSL 繫結。 
- 建立使用新憑證的新 IP 為主 SSL 繫結。

### <a name="unable-to-delete-a-certificate"></a>無法刪除憑證 

### <a name="symptom"></a>徵狀

當您嘗試刪除憑證時，收到下列錯誤訊息：

**無法刪除憑證，因為 SSL 繫結正在使用中。您必須先移除 SSL 繫結，才可以刪除憑證。**

### <a name="cause"></a>原因

如果有另一個 Web 應用程式使用憑證，可能會出現這個問題。

### <a name="solution"></a>解決方法

從 Web 應用程式移除該憑證的 SSL 繫結。 接著再嘗試刪除憑證。 如果您仍然無法刪除憑證，請清除網際網路瀏覽器快取，然後在新的瀏覽器視窗中重新開啟 Azure 入口網站。 接著再嘗試刪除憑證。

### <a name="unable-to-purchase-an-app-service-certificate"></a>無法購買 App Service 憑證 

### <a name="symptom"></a>徵狀
您無法從 Azure 入口網站購買 [App Service 憑證](./web-sites-purchase-ssl-web-site.md)。

### <a name="cause-and-solution"></a>原因和解決方案
以下是這個問題發生的原因：

- App Service 方案是「免費」或「共用」。 這些定價層不支援 SSL。 

    **解決方案**：將 Web 應用程式的 App Service 方案升級為「標準」。

- 訂用帳戶的信用卡無效。

    **解決方案**：為訂用帳戶新增有效的信用卡。 

- 訂用帳戶優惠不支援購買 Microsoft Student 之類的 App Service 憑證。  

    **解決方案**：升級您的訂用帳戶。 

- 訂用帳戶已達到單一訂用帳戶允許的購買上限。

    **解決方案**：預付型方案和 EA 訂用帳戶類型的 App Service 憑證有購買 10 個憑證的限制。 其他訂用帳戶類型的限制則為 3 個。 若要提高限制，請連絡 [Azure支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- App Service 憑證已標示為詐騙。 您收到以下錯誤訊息：「您的憑證已被標幟為可能是詐騙。 要求目前正在進行檢閱。 如果憑證在 24 小時內無法使用」。

    **解決方案**：如果憑證被標示為詐騙，而且在接下來的 24 小時內未能解決，請遵循以下步驟：

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 前往 [App Service 憑證]，然後選取憑證。
    3. 選取 [憑證組態] > 步驟 2：[驗證] > [網域驗證]。 這會傳送電子郵件通知給 Azure 憑證提供者，尋求解決問題。

## <a name="domain-problems"></a>網域問題

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>錯買網域的 SSL 憑證

### <a name="symptom"></a>徵狀

您錯買網域的 App Service 憑證，而且無法更新憑證以使用正確的網域。

### <a name="solution"></a>解決方法

- 刪除憑證，然後再購買新憑證。
- 如果使用錯誤網域的現行憑證處於「已發行」狀態，您也必須支付該憑證的費用。 App Service 憑證無法退款，不過您可以連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，看看是否有其他選擇。 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>App Service 憑證已更新，不過仍然顯示舊憑證 

### <a name="symptom"></a>徵狀

App Service 憑證已更新，不過使用 App Service 憑證的 Web 應用程式仍然使用舊憑證。 此外，您收到必須使用 HTTPS 通訊協定的警告。

### <a name="cause"></a>原因 
Web 應用程式會每隔八小時執行一次背景作業，如果憑證資源發生變更，便會進行同步處理。 因此，當您輪替或更新憑證時，有時候應用程式仍會擷取舊憑證，而非更新後的憑證。 這是因為同步處理憑證資源的作業尚未執行。 
 
### <a name="solution"></a>解決方法

您可以強制同步處理憑證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [App Service 憑證]，然後選取憑證。
2. 按一下 [重設金鑰與同步]，然後按一下 [同步]。這項作業需要一些時間才能完成。 
3. 當同步完成時，您會看見以下通知：「已成功將所有資源更新為最新憑證」。

### <a name="domain-verification-is-not-working"></a>網域驗證無法運作 

### <a name="symptom"></a>徵狀 
App Service 憑證必須經過網域驗證才能使用。 當您按一下 [驗證] 時，程序會失敗。

### <a name="solution"></a>解決方法
新增 TXT 記錄來手動驗證網域：
 
1.  前往主控網域名稱的網域名稱服務 (DNS) 提供者。
2.  為您的網域新增一筆 TXT 記錄，並且使用 Azure 入口網站中顯示的網域權杖值。 

請稍候幾分鐘等待 DNS 傳播執行，然後按一下 [重新整理] 按鈕來觸發驗證。 

「HTML 網頁方法」是進行手動驗證的替代方法，能用來讓憑證授權單位確認憑證發行目標網域的網域擁有權。

1.  建立名為 {Domain Verification Token}.html 的 HTML 檔案。 
2.  此檔案的內容應為網域驗證權杖的值。
3.  在主控網域的 Web 伺服器根目錄上傳此檔案
4.  按一下 [重新整理] 檢查憑證狀態。 驗證可能需要數分鐘才能完成。

例如，如果您為 azure.com 購買網域驗證權杖為 ‘1234abcd’ 的標準憑證，驗證作業就會向 http://azure.com/1234abcd.html 發出 Web 要求，並且應傳回 1234abcd。 

> [!IMPORTANT]
> 憑證訂購必須在 15 天內完成網域驗證作業。 15 天過後，憑證授權單位就會拒絕憑證，您也不需要支付該憑證的費用。 在這種情況下，請刪除該憑證，然後再試一次。
>
> 

### <a name="unable-to-purchase-a-domain"></a>無法購買網域

### <a name="symptom"></a>徵狀
您無法從 Web 應用程式購買網域，或在 Azure 入口網站中購買 App Service 網域。

### <a name="cause-and-solution"></a>原因和解決方案

這個問題發生的原因如下：

- Azure 訂用帳戶沒有信用卡或信用卡無效。

    **解決方案**：如果訂用帳戶沒有信用卡，請新增有效的信用卡。

- 如果您不是訂用帳戶擁有者，可能沒有購買網域的權限。

    **解決方案**：[將擁有者角色新增](../billing/billing-add-change-azure-subscription-administrator.md)至帳戶，或連絡訂用帳戶管理員，索取購買網域的權限。
- 您的訂用帳戶已達到購買網域的上限。 目前的限制為 20 個。

    **解決方案**：若要申請提高限制，請連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 您的 Azure 訂用帳戶類型不支援購買 App Service 網域。

    **解決方案**：將 Azure 訂用帳戶升級為其他訂用帳戶類型，如預付型方案訂用帳戶。

### <a name="unable-to-add-a-hostname-to-web-app"></a>無法將主機名稱新增至 Web 應用程式 

### <a name="symptom"></a>徵狀

當您新增主機名稱時，程序無法驗證及確認網域。

### <a name="cause"></a>原因 

這個問題發生的原因如下：

- 您無權新增主機名稱。

    **解決方案**：請洽詢訂用帳戶管理員，確認您擁有新增主機名稱的權限。
- 您的網域擁有權無法驗證。

    **解決方案**：確認您的 CNAME 或 A 記錄設定正確。 若要將自訂網域對應至 Web 應用程式，請建立 CNAME 或 A 記錄。 如果您想要使用根網域，必須使用 A 和 TXT 記錄：

    |記錄類型|Host|指向|
    |------|------|-----|
    |具有使用 |@|IP 位址或 Web 應用程式|
    |TXT|@|<app-name>.azurewebsites.net|
    |CNAME|www|<app-name>.azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>DNS 無法解析

### <a name="symptom"></a>徵狀

您收到「找不到 DNS 記錄」錯誤訊息。

### <a name="cause"></a>原因
這個問題發生的原因如下：

- 存留時間 (TTL) 期間尚未到期。 請檢查網域的 DNS 組態以判斷 TTL 值，然後等待期間到期。
- DNS 組態不正確。

### <a name="solution"></a>解決方法
- 等候 48 小時讓該問題自行解決。
- 如果您可以變更 DNS 組態中的 TTL 設定，請將值變更為 5 分鐘，看看是否能解決問題。
- 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 來確認網域是否指向 Web 應用程式 IP 位址。 如果情況並非如此，請設定 A 記錄來更正 Web 應用程式的 IP 位址。

### <a name="restore-a-deleted-domain"></a>還原已刪除的網域 

### <a name="symptom"></a>徵狀
您的網域不再顯示於 Azure 入口網站中。

### <a name="cause"></a>原因 
訂用帳戶的擁有者可能已意外刪除網域。

### <a name="solution"></a>解決方法
如果網域遭到刪除的時間距離今天不到七天，網域的刪除程序可能尚未開始。 此時，您可以 Azure 入口網站中以同一個訂用帳戶再次購買相同的網域 (請務必在搜尋方塊內輸入完全相同的網域名稱)。 您將不需要再支付這個網域的費用。 如果網域的刪除時間距離今天已超過七天，請連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)尋求網域還原協助。

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>自訂網域傳回 404 或網站無法存取 

### <a name="symptom"></a>徵狀

當您使用自訂網域名稱瀏覽至往暫時，收到以下錯誤訊息：

**錯誤 404 - 找不到 Web 應用程式。**


### <a name="cause-and-solution"></a>原因和解決方案

**原因 1** 

您設定的自訂網域遺漏 CNAME 或 A 記錄。 

**原因 1 的解決方案**

- 如果您已新增 A 記錄，請務必一併新增 TXT 記錄。 如需詳細資訊，請參閱 [Create-the-a-record](./app-service-web-tutorial-custom-domain.md#create-the-a-record)。
- 如果您的 Web 應用程式不一定要使用根網域，建議您使用 CNAME 記錄，避免使用 A 記錄。
- CNAME 和 A 記錄不應同時用於同一個網域。 這樣可能會導致衝突，使網域無法解析。 

**原因 2** 

網際網路瀏覽器快取的網域可能還是舊有的 IP 位址。 

**原因 2 的解決方案**

清除瀏覽器。 如果您使用 Windows 裝置，可以執行命令 `ipconfig /flushdns`。 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 來確認網域是否指向 Web 應用程式 IP 位址。 

### <a name="unable-to-add-subdomain"></a>無法新增子網域 

### <a name="symptom"></a>徵狀

您無法將新主機名稱新增至 Web 應用程式來指派子網域。

### <a name="solution"></a>解決方法

- 請洽詢訂用帳戶管理員，確認您擁有將主機名稱新增至 Web 應用程式的權限。
- 如果您需要更多子網域，建議您將網域主機代管變更為 Azure DNS。 只要使用 Azure DNS，您就可以將 500 個主機名稱新增至 Web 應用程式。 如需詳細資訊，請參閱[新增子網域](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)。











 


















