---
title: Azure AD 密碼保護預覽
description: 使用 Azure AD 密碼保護 (預覽) 在內部部署 Active Directory 中禁用弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415743"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>預覽：強制執行 Windows Server Active Directory 的 Azure AD 密碼保護

|     |
| --- |
| Azure AD 密碼保護和自訂的禁用密碼清單是 Azure Active Directory 的公開預覽版功能。 如需預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Azure AD 密碼保護是 Azure Active Directory (Azure AD) 的公開預覽版中提供的新功能，可強化組織中的密碼原則。 Azure AD 密碼保護的內部部署同時使用了儲存在 Azure AD 中的全域和自訂禁用密碼清單，並在內部部署執行與 Azure AD 雲端式變更相同的檢查。

## <a name="design-principles"></a>設計原則

設計 Active Directory 的 Azure AD 密碼保護時會考量以下原則：

* 網域控制站永遠不需要直接與網際網路通訊
* 在網域控制站上不會開啟任何新的網路連接埠。
* 不需要 Active Directory 結構描述變更。 軟體會使用現有的 Active Directory 容器和 serviceConnectionPoint 結構描述物件。
* 不需要基本的 Active Directory 網域或樹系功能層級 (DFL\FFL)。
* 軟體在其保護的 Active Directory 網域中不會建立或需要任何帳戶。
* 使用者純文字密碼絕不能離開網域控制站 (不論在密碼驗證作業期間，或在任何其他時間)。
* 支援依權衡方式僅對安裝網域控制站代理程式的機器強制執行密碼原則的累加部署。
* 建議您在所有 DC 上安裝 DC 代理程式，以確保無所不在的密碼保護安全性施行。

## <a name="architectural-diagram"></a>架構圖

請務必在內部部署 Active Directory 環境中部署 Azure AD 密碼保護之前，了解基礎設計和功能性概念。 下圖顯示 Azure AD 密碼保護的元件如何一起運作：

![Azure AD 密碼保護元件搭配運作的方式](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

上圖顯示構成 Azure AD 密碼保護的三個基本軟體元件：

* Azure AD 密碼保護 Proxy 服務會在目前的 Active Directory 樹系中任何已加入網域的機器上執行。 其主要目的是將來自網域控制站的密碼原則下載要求轉送至 Azure AD，並將 Azure AD 的回應傳回至網域控制站。
* Azure AD 密碼保護 DC 代理程式密碼篩選 dll 會接收來自作業系統的使用者密碼驗證要求，並將其轉送至在網域控制站上本機執行的 Azure AD 密碼保護 DC 代理程式服務。
* Azure AD 密碼保護 DC 代理程式服務會接收來自 DC 代理程式密碼篩選 dll 的密碼驗證要求，使用目前 (本機可用) 的密碼原則加以處理，然後傳回結果 (通過\失敗)。

## <a name="theory-of-operations"></a>作業原理

假設上述圖表和設計原則，Azure AD 密碼保護的實際運作方式為何？

每個 Azure AD 密碼保護 Proxy 服務都會在 Active Directory 中建立 serviceConnectionPoint 物件，以對樹系中的網域控制站通告本身。

每個 Azure AD 密碼保護 DC 代理程式服務也會在 Active Directory 中建立 serviceConnectionPoint 物件。 不過這主要用於報告和診斷。

Azure AD 密碼保護 DC 代理程式服務負責起始從 Azure AD 下載新密碼原則。 第一個步驟是藉由查詢樹系中的 Proxy serviceConnectionPoint 物件，以找出 Azure AD 密碼保護 Proxy 服務。 一旦找到可用的 Proxy 服務，密碼原則下載要求就會從 DC 代理程式服務傳送到 Proxy 服務，接著將該要求傳送至 Azure AD，然後將回應傳回給 DC 代理程式服務。 從 Azure AD 接收新密碼原則之後，DC 代理程式服務會將此原則儲存在其網域 sysvol 共用根目錄的專用資料夾中。 如果從網域中其他 DC 代理程式服務複寫較新的原則，DC 代理程式服務也會監視此資料夾。

Azure AD 密碼保護 DC 代理程式服務一律會在服務啟動時要求新的原則。 在 DC 代理程式服務啟動之後，它會定期 (每小時一次) 檢查目前本機可用原則的存留時間；如果目前的原則存留超過一小時，DC 代理程式服務會如上所述向 Azure AD 要求新原則，否則 DC 代理程式會繼續使用目前的原則。

Azure AD 密碼保護 DC 代理程式服務會使用「透過 TCP 的 RPC (遠端程序呼叫)」，來與 Azure AD 密碼保護 Proxy 服務通訊。 Proxy 服務會在動態或靜態 RPC 連接埠 (依設定) 上接聽這些呼叫。

Azure AD 密碼保護 DC 代理程式永遠不會在可連上網路的連接埠上接聽，而 Proxy 服務永遠不會嘗試呼叫 DC 代理程式服務。

Azure AD 密碼保護 Proxy 服務為無狀態；它永遠不會快取從 Azure 下載的原則或任何其他狀態。

Azure AD 密碼保護 DC 代理程式服務只會使用最新的本機可用密碼原則來評估使用者的密碼。 如果本機網域控制站沒有可用的密碼原則，系統將會自動接受密碼，而且會記錄事件記錄訊息來警告系統管理員。

Azure AD 密碼保護不是即時原則應用程式引擎。 從在 Azure AD 中進行密碼原則組態變更後，到變更同步至所有網域控制站並強制執行時，可能會有時間上的延遲。

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Azure AD 密碼保護的樹系\租用戶繫結

在 Active Directory 樹系中部署 Azure AD 密碼保護時，需要向 Azure AD 註冊 Active Directory 樹系，以及任何已部署的 Azure AD 密碼保護 Proxy 服務。 這兩項註冊 (樹系和 Proxy) 會與特定 Azure AD 租用戶相關聯，該租用戶會以隱含方式透過註冊期間所用的認證識別。 下載 Azure AD 密碼保護密碼原則之後，該原則一律由此租用戶專用 (也就是說，該原則一律會結合 Microsoft 全球禁用密碼清單和每一租用戶自訂禁用密碼清單)。 不支援在樹系中設定要繫結至不同 Azure AD 租用戶的不同網域或 Proxy。

## <a name="license-requirements"></a>授權需求

Azure Active Directory (Azure AD) 的所有使用者均可受益於全域禁用密碼清單的功效。

自訂禁用密碼清單需要 Azure AD Basic 授權。

Windows Server Active Directory 的 Azure AD 密碼保護需要 Azure AD Premium 授權。

在 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到其他授權資訊 (包括成本)。

## <a name="download"></a>下載

從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)可以下載 Azure AD 密碼保護的兩個必要代理城市安裝程式

## <a name="next-steps"></a>後續步驟

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
