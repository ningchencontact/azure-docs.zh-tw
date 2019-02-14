---
title: Azure Active Directory Identity Protection 風險事件參考 | Microsoft Docs
description: Azure Active Directory Identity Protection 風險事件參考。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1509f40b88e3dc9c51bd00ed379c5b0130230a99
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178833"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Azure Active Directory Identity Protection 風險事件參考

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 探索遭入侵的身分識別並不容易。 Azure Active Directory 使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 偵測到的每個可疑動作都會儲存在名為風險事件的記錄中。


## <a name="anonymous-ip-address"></a>匿名 IP 位址

**偵測類型：** 即時  
**舊名稱：** 從匿名 IP 位址登入


此風險事件類型指出從匿名 IP 位址執行的登入 (例如 Tor 瀏覽器、Anonymizer VPN)。
這些 IP 位址通常由意圖隱藏其登入遙測資料 (IP 位址、位置、裝置等) 的執行者所使用，以遂行其潛在惡意目的。


## <a name="atypical-travel"></a>非慣用登入位置

**偵測類型：** 離線  
**舊名稱：** 不可能到達非典型位置的移動


此風險事件類型會識別來自距離遙遠的位置的兩次登入，而根據使用者過去的行為，其中至少有一個位置可能不尋常。 在許多其他因素中，此機器學習演算法會考量兩次登入之間的時間，以及使用者從第一個位置移到第二個位置所需的時間，這表示有不同的使用者正在使用相同的認證。

演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。 系統有前 14 天或 10 次登入的初始學習期間，它會在這段期間了解新使用者的登入行為。


## <a name="leaked-credentials"></a>認證外洩

**偵測類型：** 離線  
**舊名稱：** 認證外洩的使用者


此風險事件類型指出使用者的有效認證已外洩。
當網路罪犯入侵合法使用者的有效密碼時，罪犯通常會共用這些認證。 其做法通常是將認證公開張貼在暗網或分享網站上，或是在黑市上交易或銷售認證。 Microsoft 的認證外洩服務取得使用者名稱 / 密碼組的方式，是監視公用及深層網路，以及使用：

- 研究員

- 執法機關

- Microsoft 安全性小組

- 其他受信任的來源

服務從暗網、分享網站或前述來源取得使用者認證時，將會根據 Azure AD 使用者目前的有效認證對其進行檢查，以尋找有效的相符項目。


## <a name="malware-linked-ip-address"></a>已連結惡意程式碼的 IP 位址

**偵測類型：** 離線  
**舊名稱：** 從受感染的裝置登入


此風險事件類型指出從感染惡意程式碼的 IP 位址 (會主動與 Bot 伺服器通訊) 執行的登入。 讓使用者裝置的 IP 位址與在 Bot 伺服器作用期間聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。


## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登入屬性

**偵測類型：** 即時  
**舊名稱：** 從不熟悉的位置登入

此風險事件類型會考量過去的登入屬性 (例如裝置、位置、網路)，以判定具有不熟悉屬性的登入。 系統會儲存使用者先前所用位置的屬性，並考量這些「熟悉的」位置。 使用不在熟悉屬性清單中的屬性登入時，就會觸發此風險事件。 系統有為期 30 天的初始學習期間，在這段期間內，它不會標示任何新的偵測。
我們也會針對基本驗證 (或舊版通訊協定) 執行這項偵測。 由於這些通訊協定沒有用戶端識別碼之類的新式屬性，因此只能以有限的遙測資料減少誤判。 我們建議客戶移轉至新式驗證。

