---
title: 偵錯應用程式 Proxy 應用程式-Azure Active Directory |Microsoft Docs
description: 偵錯與 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式的問題。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172261"
---
# <a name="debug-application-proxy-application-issues"></a>偵錯應用程式 Proxy 應用程式問題 

這篇文章可協助您針對與 Azure Active Directory (Azure AD) 應用程式 Proxy 應用程式的問題進行疑難排解。 如果您使用的內部部署 web 應用程式中，遠端存取的應用程式 Proxy 服務，但您無法連線到應用程式，請使用此流程圖的應用程式問題進行偵錯。 

## <a name="before-you-begin"></a>開始之前

當應用程式 Proxy 針對問題進行疑難排解，我們建議您開始使用連接器。 首先，請依照中的疑難排解流程[偵錯應用程式 Proxy 連接器發出](application-proxy-debug-connectors.md)先確定已正確設定應用程式 Proxy 連接器。 如果您仍遇到問題，請回到本文，以疑難排解應用程式。  

如需有關應用程式 Proxy 的詳細資訊，請參閱：

- [在內部部署應用程式，透過應用程式 Proxy 遠端存取](application-proxy.md)
- [應用程式 Proxy 連接器](application-proxy-connectors.md)
- [安裝並註冊連接器](application-proxy-add-on-premises-application.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>流程圖的應用程式問題

此流程圖會引導您進行偵錯一些較常見的問題，連線到應用程式的步驟。 如需每個步驟的詳細資訊，請參閱下列流程圖。

![示範適用於偵錯應用程式步驟的流程圖](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  |  動作 | 說明 | 
|---------|---------|---------|
|1 | 開啟瀏覽器，存取應用程式，並輸入您的認證 | 請嘗試使用您的認證來登入應用程式，並檢查任何使用者相關的錯誤，例如[無法存取此公司的應用程式](application-proxy-sign-in-bad-gateway-timeout-error.md)。 |
|2 | 確認使用者指派至應用程式 | 請確定您的使用者帳戶有權存取公司網路內的應用程式，然後測試 登入應用程式中的步驟[測試應用程式](application-proxy-add-on-premises-application.md#test-the-application)。 如果登入問題持續發生，請參閱[如何疑難排解登入錯誤](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors)。  |
|3 | 開啟瀏覽器，然後再次嘗試存取應用程式 | 如果立即出現錯誤，請檢查是否已正確設定應用程式 Proxy。 如需有關特定錯誤訊息的詳細資訊，請參閱 <<c0> [ 疑難排解應用程式 Proxy 問題和錯誤訊息](application-proxy-troubleshoot.md)。  |
|4 | 請檢查您的自訂網域設定，或對錯誤進行疑難排解 | 如果頁面未完全顯示，請確定您的自訂網域已正確設定，藉由檢閱[使用的自訂網域](application-proxy-configure-custom-domain.md)。<br></br>如果頁面就不會載入，而且會出現錯誤訊息，對錯誤進行疑難排解的參考[疑難排解應用程式 Proxy 問題和錯誤訊息](application-proxy-troubleshoot.md)。 <br></br>如果要花超過 20 秒才會出現錯誤訊息，可能有連線問題。 移至[偵錯應用程式 Proxy 連接器](application-proxy-debug-connectors.md)疑難排解文章。  |
|5 | 如果問題持續發生，請移至偵錯的連接器 | 可能有 proxy 與連接器之間，或連接器和後端之間的連線發生問題。 移至[偵錯應用程式 Proxy 連接器](application-proxy-debug-connectors.md)疑難排解文章。 |
|6 | 發行的所有資源、 檢查瀏覽器開發人員工具，並修正連結 | 請確定發行路徑中包含所有必要的映像、 指令碼和您的應用程式的樣式表。 如需詳細資訊，請參閱 <<c0> [ 新增至 Azure AD 的內部部署應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 <br></br>使用瀏覽器的開發人員工具 （在 Internet Explorer 或 Microsoft Edge 的 F12 工具） 和檢查發佈問題中所述[應用程式頁面未正確顯示](application-proxy-page-appearance-broken-problem.md)。 <br></br>檢閱解決中斷的連結選項[頁面上的連結沒有作用](application-proxy-page-links-broken-problem.md)。 |
|7 | 檢查有網路延遲 | 如果頁面載入緩慢，了解如何在網路延遲降至最低[降低延遲的考量](application-proxy-network-topology.md#considerations-for-reducing-latency)。 | 
|8 | 請參閱其他的疑難排解說明 | 如果問題持續發生，找到其他的疑難排解文件中[疑難排解文件的應用程式 Proxy](application-proxy-page-appearance-broken-problem.md)。 |

## <a name="next-steps"></a>後續步驟


* [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
* [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
* [如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](application-proxy-register-connector-powershell.md)
