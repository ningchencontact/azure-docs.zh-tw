---
title: Azure AD 應用程式 Proxy：版本發行歷程記錄 | Microsoft Docs
description: 本文列出 Azure AD 應用程式 Proxy 的所有版本, 並說明新功能和已修正的問題
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693908"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 應用程式 Proxy：版本發行歷程記錄
本文列出已發行的 Azure Active Directory (Azure AD) 應用程式 Proxy 的版本和功能。 Azure AD 小組會定期以新的特性和功能更新應用程式 Proxy。 發行新版本時, 會自動更新應用程式 Proxy 連接器。 

我們建議您確定已為您的連接器啟用自動更新, 以確保您擁有最新的功能和 bug 修正。 Microsoft 會提供最新連接器版本的直接支援, 以及一個先前的版本。

以下是相關資源的清單:

Resource |  詳細資料
--------- | --------- |
如何啟用應用程式 Proxy | 本[教學](application-proxy-add-on-premises-application.md)課程會說明啟用應用程式 Proxy 以及安裝和註冊連接器的必要條件。
了解 Azure AD 應用程式 Proxy 連接器 | 深入瞭解[連接器管理](application-proxy-connectors.md), 以及連接器如何[自動升級](application-proxy-connectors.md#automatic-updates)。
Azure AD 應用程式 Proxy 連接器下載 |  [下載最新的連接器](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>發行狀態

2018年9月20日:已發行可供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 已新增 QlikSense 應用程式的 WebSocket 支援。 若要深入瞭解如何整合 QlikSense 與應用程式 Proxy, 請參閱此[逐步](application-proxy-qlik.md)解說。 
- 已改善安裝精靈, 讓您更輕鬆地設定輸出 proxy。 
- 將 TLS 1.2 設定為連接器的預設通訊協定。 
- 已加入新的使用者授權合約 (EULA)。  

### <a name="fixed-issues"></a>已修正的問題

- 已修正導致連接器中的記憶體流失的 bug。
- 已更新 Azure 服務匯流排版本, 其中包含連接器超時問題的錯誤修正。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>發行狀態

2018年1月19日:已發行可供下載

### <a name="fixed-issues"></a>已修正的問題

- 已新增對 cookie 中需要網域轉譯的自訂網域的支援。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>發行狀態 

2017 5 月25日:已發行可供下載 

### <a name="new-features-and-improvements"></a>新功能和改進 

改善對連接器的輸出連線限制的控制。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>發行狀態

2017年4月15日:已發行可供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 使用較少的必要端口來簡化上架和管理。 應用程式 Proxy 現在只需要開啟兩個標準輸出埠:443和80。 應用程式 Proxy 只會繼續使用輸出連線, 因此您仍然不需要 DMZ 中的任何元件。 如需詳細資訊, 請參閱我們的設定 [檔](application-proxy-add-on-premises-application.md)。  
- 如果您的外部 proxy 或防火牆支援, 現在您可以透過 DNS 而不是 IP 範圍來開啟您的網路。 應用程式 Proxy 服務只需要連接 *. msappproxy.net 和 *. servicebus.windows.net。


## <a name="earlier-versions"></a>較早的版本

如果您使用的應用程式 Proxy 連接器版本早于 1.5.36.0, 請更新為最新版本, 以確保您擁有最新的完整支援功能。

## <a name="next-steps"></a>後續步驟
- 深入瞭解[如何透過 Azure AD 應用程式 Proxy 來遠端存取內部部署應用程式](application-proxy.md)。
- 若要開始使用應用程式 Proxy，請參閱[教學課程：新增內部部署應用程式以便透過應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)。
