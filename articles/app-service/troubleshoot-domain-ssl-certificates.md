---
title: 針對網域和 SSL 憑證問題進行疑難排解 - Azure App Service | Microsoft Docs
description: 在 Azure App Service 中對網域和 SSL 憑證問題進行疑難排解
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
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: c0584a69349c2785b5b6bce1d17c023c95b36151
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525376"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>在 Azure App Service 中對網域和 SSL 憑證問題進行疑難排解

本文列出在 Azure App Service 中為 Web 應用程式設定網域或 SSL 憑證時，可能會遇到的常見問題。 文中也會描述這些問題的可能原因和解決方案。

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>憑證問題

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>您無法對應用程式新增 SSL 憑證繫結 

#### <a name="symptom"></a>徵狀

當您新增 SSL 繫結時，收到下列錯誤訊息：

「無法新增 SSL 繫結。 無法為現有 VIP 設定憑證，因為已經有另一個 VIP 使用該憑證。」

#### <a name="cause"></a>原因

多個應用程式之間，如果有多個基於相同 IP 位址的 IP 為主 SSL 繫結，可能會發生此問題。 例如，應用程式 A 的以 IP 為主 SSL 使用舊憑證。 應用程式 B 的以 IP 為主 SSL 使用同一個 IP 位址的新憑證。 當您更新使用新憑證的應用程式 SSL 繫結時，由於另一個應用程式也使用相同的 IP 位址，因此更新會失敗，而且會出現這個錯誤。 

#### <a name="solution"></a>解決方法 

若要修正問題，請使用下列其中一種方法：

- 在使用舊憑證的應用程式上刪除以 IP 為主的 SSL 繫結。 
- 建立使用新憑證的新 IP 為主 SSL 繫結。

### <a name="you-cant-delete-a-certificate"></a>您無法刪除憑證 

#### <a name="symptom"></a>徵狀

當您嘗試刪除憑證時，收到下列錯誤訊息：

「無法刪除憑證，因為 SSL 繫結正在使用中。 您必須先移除 SSL 繫結，才可以刪除憑證。」

#### <a name="cause"></a>原因

如果有另一個應用程式使用憑證，可能會出現這個問題。

#### <a name="solution"></a>解決方法

從應用程式移除該憑證的 SSL 繫結。 接著再嘗試刪除憑證。 如果您仍然無法刪除憑證，請清除網際網路瀏覽器快取，然後在新的瀏覽器視窗中重新開啟 Azure 入口網站。 接著再嘗試刪除憑證。

### <a name="you-cant-purchase-an-app-service-certificate"></a>您無法購買 App Service 憑證 

#### <a name="symptom"></a>徵狀
您無法從 Azure 入口網站購買 [Azure App Service 憑證](./web-sites-purchase-ssl-web-site.md)。

#### <a name="cause-and-solution"></a>原因和解決方案
以下是這個問題發生的原因：

- App Service 方案為免費或共用。 這些定價層不支援 SSL。 

    **解決方案**：將應用程式的 App Service 方案升級為「標準」。

- 訂用帳戶的信用卡無效。

    **解決方案**：為訂用帳戶新增有效的信用卡。 

- 訂用帳戶供應項目不支援購買 Microsoft Student 之類的 App Service 憑證。  

    **解決方案**：升級您的訂用帳戶。 

- 訂用帳戶已達到單一訂用帳戶允許的購買限制。

    **解決方案**：預付型方案和 EA 訂用帳戶類型的 App Service 憑證有購買 10 個憑證的限制。 其他訂用帳戶類型的限制則為 3 個。 若要提高限制，請連絡 [Azure支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- App Service 憑證已標示為詐騙。 您收到下列錯誤訊息：「您的憑證已標示為可能的詐欺。 要求目前正在進行檢閱。 如果憑證未變為可用在 24 小時內，請連絡 Azure 支援。 」

    **解決方案**：如果憑證已標示為詐騙，且在 24 小時後仍未解決，請依照下列步驟進行操作：

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 前往 [App Service 憑證]，然後選取憑證。
    3. 選取 [憑證組態] > [步驟 2：驗證] > [網域驗證]。 此步驟會傳送電子郵件通知給 Azure 憑證提供者，尋求解決問題。

## <a name="custom-domain-problems"></a>自定义域问题

### <a name="a-custom-domain-returns-a-404-error"></a>自訂網域傳回 404 錯誤 

#### <a name="symptom"></a>徵狀

當您使用自訂網域名稱瀏覽至往暫時，收到以下錯誤訊息：

「錯誤 404 - 找不到 Web 應用程式。」

#### <a name="cause-and-solution"></a>原因和解決方案

**原因 1** 

您設定的自訂網域遺漏 CNAME 或 A 記錄。 

**原因 1 的解決方案**

- 如果您已新增 A 記錄，請務必一併新增 TXT 記錄。 如需詳細資訊，請參閱[建立 A 記錄](./app-service-web-tutorial-custom-domain.md#create-the-a-record)。
- 如果您的應用程式不一定要使用根網域，建議您使用 CNAME 記錄，避免使用 A 記錄。
- CNAME 記錄和 A 記錄不應同時用於同一個網域。 此问题可能会导致冲突，并阻止域解析。 

**原因 2** 

網際網路瀏覽器快取的網域可能還是舊有的 IP 位址。 

**原因 2 的解決方案**

清除瀏覽器。 如果您使用 Windows 裝置，可以執行命令 `ipconfig /flushdns`。 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 來確認網域是否指向應用程式的 IP 位址。 

### <a name="you-cant-add-a-subdomain"></a>您無法新增子網域 

#### <a name="symptom"></a>徵狀

您無法將新主機名稱新增至應用程式來指派子網域。

#### <a name="solution"></a>解決方法

- 請洽詢訂用帳戶管理員，確認您擁有將主機名稱新增至應用程式的權限。
- 如果需要更多子域，我们建议将域托管服务更改为 Azure 域服务 (DNS)。 只要使用 Azure DNS，您就可以將 500 個主機名稱新增至應用程式。 如需詳細資訊，請參閱[新增子網域](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)。

### <a name="dns-cant-be-resolved"></a>無法解析 DNS

#### <a name="symptom"></a>徵狀

您收到下列錯誤訊息：

「找不到 DNS 記錄。」

#### <a name="cause"></a>原因
這個問題發生的原因如下：

- 存留時間 (TTL) 期間尚未到期。 請檢查網域的 DNS 組態以判斷 TTL 值，然後等待期間到期。
- DNS 組態不正確。

#### <a name="solution"></a>解決方法
- 等候 48 小時讓該問題自行解決。
- 如果您可以變更 DNS 組態中的 TTL 設定，請將值變更為 5 分鐘，看看是否能解決問題。
- 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 來確認網域是否指向應用程式的 IP 位址。 如果情況並非如此，請設定 A 記錄來更正應用程式的 IP 位址。

### <a name="you-need-to-restore-a-deleted-domain"></a>您必須還原已刪除的網域 

#### <a name="symptom"></a>徵狀
您的網域不再顯示於 Azure 入口網站中。

#### <a name="cause"></a>原因 
訂用帳戶的擁有者可能已意外刪除網域。

#### <a name="solution"></a>解決方法
如果網域遭到刪除的時間距離今天不到七天，網域的刪除程序可能尚未開始。 此時，您可以 Azure 入口網站中以同一個訂用帳戶再次購買相同的網域。 (請務必在搜尋方塊內輸入完全相同的網域名稱。)您將不需要再支付這個網域的費用。 如果该域的删除时间超过七天，请求助 [Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来还原该域。

## <a name="domain-problems"></a>網域問題

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>您買到錯誤網域的 SSL 憑證

#### <a name="symptom"></a>徵狀

您買到錯誤網域的 App Service 憑證。 您無法更新憑證以使用正確網域。

#### <a name="solution"></a>解決方法

刪除憑證，然後再購買新憑證。

如果使用錯誤網域的現行憑證處於「已發行」狀態，則您也必須支付該憑證的費用。 App Service 憑證無法退款，不過您可以連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，看看是否有其他選擇。 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>App Service 憑證已更新，不過應用程式顯示舊憑證 

#### <a name="symptom"></a>徵狀

App Service 憑證已更新，不過使用 App Service 憑證的應用程式仍然使用舊憑證。 此外，您收到必須使用 HTTPS 通訊協定的警告。

#### <a name="cause"></a>原因 
Azure App Service 會每隔八小時執行一次背景作業，如果憑證資源發生變更，便會進行同步處理。 當您輪替或更新憑證時，有時候應用程式仍會擷取舊憑證，而非更新後的憑證。 這是因為同步處理憑證資源的作業尚未執行。 
 
#### <a name="solution"></a>解決方法

您可以強制同步處理憑證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [App Service 憑證]，然後選取憑證。
2. 選取 [重設金鑰與同步]，然後選取 [同步]。同步需要一些時間才能完成。 
3. 同步完成時，您會看到下列通知：「已成功以最新憑證更新所有資源。」

### <a name="domain-verification-is-not-working"></a>網域驗證無法運作 

#### <a name="symptom"></a>徵狀 
App Service 憑證必須經過網域驗證才能使用。 當您選取 [驗證] 時，程序會失敗。

#### <a name="solution"></a>解決方法
新增 TXT 記錄來手動驗證網域：
 
1.  前往主控網域名稱的網域名稱服務 (DNS) 提供者。
2.  為您的網域新增一筆 TXT 記錄，並且使用 Azure 入口網站中顯示的網域權杖值。 

請稍候幾分鐘等待 DNS 傳播執行，然後選取 [重新整理] 按鈕來觸發驗證。 

或者，您可以使用 HTML 網頁方法，以手動方式驗證您的網域。 此方法可讓憑證授權單位確認憑證發行目標網域的網域擁有權。

1.  建立名為 {domain verification token}.html 的 HTML 檔案。 此檔案的內容應為網域驗證權杖的值。
3.  在主控網域的 Web 伺服器根目錄上傳此檔案。
4.  選取 [重新整理] 檢查憑證狀態。 驗證可能需要數分鐘才能完成。

例如，如果您為 azure.com 購買網域驗證權杖為 1234abcd 的標準憑證，驗證作業就會向 https://azure.com/1234abcd.html 發出 Web 要求，並且應傳回 1234abcd。 

> [!IMPORTANT]
> 憑證訂購必須在 15 天內完成網域驗證作業。 15 天過後，憑證授權單位就會拒絕憑證，您也不需要支付該憑證的費用。 在這種情況下，請刪除該憑證，然後再試一次。
>
> 

### <a name="you-cant-purchase-a-domain"></a>您無法購買網域

#### <a name="symptom"></a>徵狀
您無法在 Azure 入口網站中購買 App Service 網域。

#### <a name="cause-and-solution"></a>原因和解決方案

這個問題發生的原因如下：

- Azure 訂用帳戶沒有信用卡或信用卡無效。

    **解決方案**：為訂用帳戶新增有效的信用卡。

- 由於您不是訂用帳戶擁有者，因此沒有購買網域的權限。

    **解決方案**：[指派擁有者角色](../role-based-access-control/role-assignments-portal.md)給您的帳戶。 或是連絡訂用帳戶管理員，以取得購買網域的權限。
- 您的訂用帳戶已達到購買網域的上限。 目前的限制為 20 個。

    **解決方案**：若要申請提高限制，請連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 您的 Azure 訂用帳戶類型不支援購買 App Service 網域。

    **解決方案**：將 Azure 訂用帳戶升級為另一種訂用帳戶類型，例如預付型方案訂用帳戶。

### <a name="you-cant-add-a-host-name-to-an-app"></a>您無法將主機名稱新增至應用程式 

#### <a name="symptom"></a>徵狀

當您新增主機名稱時，程序無法驗證及確認網域。

#### <a name="cause"></a>原因 

這個問題發生的原因如下：

- 您無權新增主機名稱。

    **解決方案**：要求訂用帳戶管理員授與您新增主機名稱的權限。
- 您的網域擁有權無法驗證。

    **解決方案**：確認您的 CNAME 或 A 記錄設定正確。 若要將自訂網域對應至應用程式，請建立 CNAME 記錄或 A 記錄。 如果您想要使用根網域，必須使用 A 和 TXT 記錄：

    |記錄類型|Host|指向|
    |------|------|-----|
    |具有使用 |@|應用程式的 IP 位址|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>常見問題集

**购买网站的自定义域后是否必须配置该域？**

通过 Azure 门户购买某个域时，应用服务应用程序会自动配置为使用该自定义域。 你不需要执行任何额外的步骤。 如需詳細資訊，請觀賞[Azure App Service 自助說明：新增自訂網域名稱](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)Channel9 上。

**是否可以使用在 Azure 门户中购买的域来指向 Azure VM？**

是，您可以指向網域的 vm。 如需詳細資訊，請參閱[使用 Azure DNS 為 Azure 服務提供自訂網域設定](../dns/dns-custom-domain.md)。

**我的域是由 GoDaddy 还是 Azure DNS 托管？**

应用服务域使用 GoDaddy 进行域注册，使用 Azure DNS 来托管域。 

**我已启用自动续订，但仍收到了有关域续订的电子邮件通知。我該怎麼辦？**

如果你已启用自动续订，则不需要执行任何操作。 电子邮件通知旨在告诉你该域即将过期，如果未启用自动续订，则需要手动续订。

**在 Azure DNS 中托管域是否要付费？**

最初的域购买费用仅适用于域注册。 除了注册费用以外，Azure DNS 还会根据用量收费。 有关详细详细，请参阅 [Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。

**我的域是之前在 Azure 门户中购买的，现在想要从 GoDaddy 托管改为 Azure DNS 托管。该如何处理？**

不一定非要迁移到 Azure DNS 托管。 如果你确实想要迁移到 Azure DNS，Azure 门户中的域管理体验会提供有关转移到 Azure DNS 的步骤信息。 如果域通过应用服务购买的，则从 GoDaddy 托管迁移到 Azure DNS 的过程相对较为顺畅。

**如果通过应用服务域购买域，是否可以在 GoDaddy 而不是 Azure DNS 中托管该域？**

从 2017 年 7 月 24 日开始，在门户中购买的应用服务域将托管在 Azure DNS 中。 如果你想要使用其他托管提供商，则必须转到其网站以获取域托管解决方案。

**是否需要支付域的隐私保护费用？**

通过 Azure 门户购买域时，可以选择免费添加隐私保护。 这是通过 Azure 应用服务购买域所能获得的权益之一。

**如果我不再想要使用我的域，是否可以获得退款？**

购买域时，你可以免费试用 5 天，在此期间，可以决定是否要继续使用。 如果在这五天内你决定不需要该域，则不会产生费用。 （.uk 域例外。 购买 .uk 域会立即产生费用，而不能获得退款）。

**是否可以在订阅中的另一个 Azure 应用服务应用中使用域？**

是。 在 Azure 门户中访问“自定义域和 SSL”边栏选项卡时，会看到购买的域。 可将应用配置为使用其中的任何域。

**是否可将域从一个订阅转移到另一个订阅？**

您可以將網域移到另一個訂用帳戶/資源群組使用[移動 AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell cmdlet。

**如果我当前没有 Azure 应用服务应用，该如何管理自定义域？**

即使没有应用服务 Web 应用，也可以管理域。 域可用于虚拟机、存储等 Azure 服务。如果你打算将域用于应用服务 Web 应用，则需要添加一个未包含在免费应用服务计划中的 Web 应用才能将域绑定到 Web 应用。

**是否可将使用自定义域的 Web 应用移到另一个订阅，或者将其从应用服务环境 v1 移到 v2？**

是的，可以在订阅之间移动 Web 应用。 请遵照[如何在 Azure 中移动资源](../azure-resource-manager/resource-group-move-resources.md)中的指导操作。 移动 Web 应用时存在一些限制。 有关详细信息，请参阅[移动应用服务资源时存在的限制](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations
)。

移动 Web 应用之后，自定义域设置中的域的主机名绑定应保持不变。 无需执行额外的步骤即可配置主机名绑定。
