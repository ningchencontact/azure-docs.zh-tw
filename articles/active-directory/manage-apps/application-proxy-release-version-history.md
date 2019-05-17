---
title: Azure AD 應用程式 Proxy：版本發行歷程記錄 | Microsoft Docs
description: 本文列出 Azure AD 應用程式 Proxy 的所有版本，並說明新功能和已修正的問題
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
ms.subservice: manage-apps
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af05769861f98ec192789d90f8a61f5b6638d1d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783296"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 應用程式 Proxy：版本發行歷程記錄
本文列出的版本和 Azure Active Directory (Azure AD) 應用程式 Proxy 的已發行的功能。 Azure AD 小組會定期以新特性和功能更新應用程式 Proxy。 發行新版本時，會自動更新應用程式 Proxy 連接器。

以下是一份相關資源：

資源 |  詳細資料
--------- | --------- |
如何啟用應用程式 Proxy | 啟用應用程式 Proxy 並安裝並註冊連接器的必要條件所述這[教學課程](application-proxy-add-on-premises-application.md)。
了解 Azure AD 應用程式 Proxy 連接器 | 深入了解[連接器管理](application-proxy-connectors.md)以及連接器[自動升級](application-proxy-connectors.md#automatic-updates)。
Azure AD 應用程式 Proxy 連接器下載 |  [下載最新的連接器](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>發行狀態

2018 年 9 月 20日日：已發行可供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 已新增的 QlikSense 應用程式的 WebSocket 支援。 若要深入了解如何使用應用程式 Proxy 整合 QlikSense，請參閱這[逐步解說](application-proxy-qlik.md)。 
- 改進安裝精靈來輕鬆地設定輸出 proxy。 
- 連接器做為預設通訊協定設定 TLS 1.2。 
- 加入新的 「 使用者授權合約 (EULA)。  

### <a name="fixed-issues"></a>已修正的問題

- 已修正的 bug 導致連接器中的某些記憶體流失。
- 更新 Azure 服務匯流排 」 版本，其中包含用於接點逾時問題的修正。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>發行狀態

2018 年 1 月 19日日：已發行可供下載

### <a name="fixed-issues"></a>已修正的問題

- 需要網域轉譯在 cookie 中的自訂網域已新增的支援。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>發行狀態 

2017: 5 月 25日日已發行可供下載 

### <a name="new-features-and-improvements"></a>新功能和改進 

改善的控制連接器的輸出連線限制的詳細資訊。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>發行狀態

2017 年 4 月 15日日：已發行可供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 簡化的上架與管理較少的必要連接埠。 應用程式 Proxy 現在需要開啟只有兩個標準的輸出連接埠：443 和 80。 應用程式 Proxy 會繼續使用只連出連線，因此您仍然不需要在 DMZ 中的任何元件。 如需詳細資訊，請參閱我們 [設定文件](application-proxy-add-on-premises-application.md)。  
- 如果支援您的外部 proxy 或防火牆，您現在可以開啟 dns 的網路，而不是 IP 範圍。 應用程式 Proxy 服務需要連線到 *。 msappproxy.net 和 *。 只有 servicebus.windows.net。


## <a name="earlier-versions"></a>較早的版本

如果您將應用程式 Proxy 連接器版本早於 1.5.36.0，更新為最新的版本，以確保您有最新完整支援的功能。

## <a name="next-steps"></a>後續步驟
- 深入了解[遠端存取內部部署應用程式可以透過 Azure AD 應用程式 Proxy](application-proxy.md)。
- 若要開始使用應用程式 Proxy，請參閱[教學課程：新增內部部署應用程式以便透過應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)。
