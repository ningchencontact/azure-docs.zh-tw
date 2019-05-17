---
title: 升級到 Azure AD 應用程式 Proxy | Microsoft Docs
description: 如果您是從 Microsoft Forefront 或 Unified Access Gateway 升級，選擇哪一個 Proxy 解決方案最適合。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a98a0394ff2a74aa256637d3c12007374200ef22
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783454"
---
# <a name="compare-remote-access-solutions"></a>比較遠端存取解決方案

Azure Active Directory 應用程式 Proxy 是 Microsoft 提供的兩個遠端存取解決方案其中之一。 另一個是 Web 應用程式 Proxy，內部部署版本。 這兩個解決方案取代了 Microsoft 提供的舊版產品：Microsoft Forefront Threat Management Gateway (TMG) 和 Unified Access Gateway (UAG)。 若要了解這四個解決方案之間的比較，可使用這份文件。 若您仍在使用已取代的 TMG 或 UAG 解決方案，使用本文章可協助您規劃移轉至其中一個應用程式 Proxy。 


## <a name="feature-comparison"></a>功能比較

您可以使用此表格來了解 Threat Management Gateway (TMG)、Unified Access Gateway (UAG)、Web 應用程式 Proxy (WAP) 和 Azure AD 應用程式 Proxy (AP) 之間的比較。

| 功能 | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| 憑證驗證 | 有 | 有 | - | - |
| 選擇性地發佈瀏覽器應用程式 | 有 | 是 | 是 | 有 |
| 預先驗證和單一登入 | 有 | 是 | 是 | 有 | 
| 第 2 層/第 3 層防火牆 | 有 | 有 | - | - |
| 轉接 Proxy 功能 | 有 | - | - | - |
| VPN 功能 | 有 | 有 | - | - |
| 豐富通訊協定支援 | - | 有 | 是，如果是透過 HTTP 執行 | 是，如果是透過 HTTP 或透過遠端桌面閘道執行 |
| 作為 ADFS Proxy 伺服器 | - | 有 | 有 | - |
| 應用程式存取的單一入口網站 | - | 有 | - | 有 |
| 回應內文連結轉譯 | 有 | 是 | - | 有 | 
| 使用標頭進行驗證 | - | 有 | - | 是，使用 PingAccess | 
| 雲端級別安全性 | - | - | - | 有 | 
| 條件式存取 | - | 有 | - | yes |
| 周邊網路 (DMZ) 中沒有任何元件 | - | - | - | yes |
| 沒有輸入連線 | - | - | - | 有 |

大部分的情況下，我們建議 Azure AD 應用程式 Proxy 作為現代化解決方案。 Web 應用程式 Proxy 只建議用在需要 AD FS Proxy 伺服器的情節中，而且您無法使用 Azure Active Directory 中的自訂網域。 

相較於類似的產品，Azure AD 應用程式 Proxy 提供獨特的優點，包括：

- 將 Azure AD 擴充至內部部署資源
   - 雲端級別安全性和保護
   - 可輕鬆地啟用諸如條件式存取和 Multi-Factor Authentication 等功能
- 周邊網路中沒有任何元件
- 沒有所需的輸入連線
- 您的使用者可以使用其所有應用程式的單一存取面板，包括 O365、Azure AD 整合式 SaaS 應用程式，以及您的內部部署 Web 應用程式。 


## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 應用程式提供對內部部署應用程式的安全遠端存取](application-proxy.md)
- [從 Forefront TMG 和 UAG 轉換至應用程式 Proxy](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)。
