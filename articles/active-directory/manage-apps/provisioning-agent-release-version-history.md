---
title: Azure AD Connect 布建代理程式：版本發行歷程記錄 | Microsoft Docs
description: 本文列出 Azure AD Connect 布建代理程式的所有版本，並說明新功能和已修正的問題
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862105"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect 布建代理程式：版本發行歷程記錄
本文列出已發行 Azure AD Connect 布建代理程式的版本和功能。 Azure AD 小組會定期以新的特性和功能更新布建代理程式。 發行新版本時，會自動更新布建代理程式。 

我們建議您為您的代理程式啟用自動更新，以確保您擁有最新的功能和 bug 修正。 Microsoft 提供最新代理程式版本的直接支援，還有一個版本。

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>發行狀態

2019年9月9日：已發行以進行自動更新

### <a name="new-features-and-improvements"></a>新功能和改進

* 能夠設定其他追蹤和記錄以進行偵錯工具布建代理程式問題
* 能夠只提取對應中設定的 AD 屬性，以改善同步處理的效能

### <a name="fixed-issues"></a>已修正的問題

* 已修正當 AD 連線失敗發生問題時，代理程式中的已進入無回應狀態的錯誤（bug）
* 修正從 Active Directory 讀取二進位資料時，造成問題的錯誤（bug）
* 已修正代理程式無法使用雲端混合式身分識別服務更新信任的錯誤（bug）

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>發行狀態

2019年1月23日：已發行可供下載

### <a name="new-features-and-improvements"></a>新功能和改進

* 改頭換面布建代理程式 & 連接器架構，以獲得更佳的效能、穩定性和可靠性 
* 使用 UI 驅動的安裝精靈簡化布建代理程式設定 
* 已新增自動代理程式更新的支援

