---
title: Azure AD 密碼保護-Azure Active Directory
description: 禁止使用 Azure AD 密碼保護的內部部署 Active Directory 中的弱式密碼
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
ms.openlocfilehash: 9cd9f6112cbca78b323e0a14818b06f891a3f673
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862882"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>強制執行 Windows Server Active Directory 的 Azure AD 密碼保護

Azure AD 密碼保護是一項功能，可增強組織中的密碼原則。 在內部部署密碼保護，會使用這兩個的全域和自訂禁用密碼清單儲存在 Azure AD。 它會執行相同檢查內部部署為 Azure AD 的雲端架構的變更。

## <a name="design-principles"></a>設計原則

Azure AD 密碼保護的設計考量這些原則：

* 網域控制站永遠不需要直接與網際網路通訊。
* 在網域控制站上不會開啟任何新的網路連接埠。
* 不需要 Active Directory 結構描述變更。 軟體會使用現有的 Active Directory**容器**並**serviceConnectionPoint**結構描述物件。
* 需要任何最低 Active Directory 網域或樹系功能等級 (DFL/FFL) 不。
* 軟體不會建立，或需要在它所保護的 Active Directory 網域中的帳戶。
* 使用者使用純文字密碼不保留網域控制站，在密碼驗證作業期間，或在任何其他的時間。
* 支援累加部署，但密碼原則只會強制執行已安裝網域控制站代理程式 （DC 代理程式）。 請參閱下一步 的主題，如需詳細資訊。

## <a name="incremental-deployment"></a>累加部署

Azure AD 密碼保護支援跨 Active Directory 網域中網域控制站的累加部署，但請務必了解真正的意義，以及它們的權衡取捨。

Azure AD 密碼保護 DC 代理程式軟體安裝在網域控制站，而且僅適用於傳送到該網域控制站的密碼變更時，可以只驗證密碼。 您不能控制的網域控制站會處理使用者密碼變更的 Windows 用戶端電腦的選擇。 為了確保一致的行為和通用的密碼保護的安全性強制，請將 DC 代理程式軟體必須安裝在網域中的所有網域控制站上。

許多組織會想要仔細測試子集上的網域控制站之前進行完整部署的 Azure AD 密碼保護。 Azure AD 密碼保護支援部分的部署、 ie DC 代理程式軟體，在指定的網域控制站會主動驗證密碼即使在網域中的其他網域控制站不需要安裝 DC 代理程式軟體。 部分的部署，這種不安全，不以外的其他建議基於測試目的。

## <a name="architectural-diagram"></a>架構圖

請務必瞭解基礎的設計和函式概念，然後再部署在內部部署 Active Directory 環境中的 Azure AD 密碼保護。 下圖顯示密碼保護的元件如何共同運作：

![Azure AD 密碼保護元件搭配運作的方式](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密碼保護 Proxy 服務會在目前的 Active Directory 樹系中任何已加入網域的機器上執行。 其主要用途是將從網域控制站的密碼原則下載要求轉送至 Azure AD。 然後傳回回應，從 Azure AD 網域控制站。
* 密碼篩選 DLL 的 DC 代理程式會從作業系統接收使用者的密碼驗證要求。 它將它們轉送到本機網域控制站執行的 DC 代理程式服務。
* 密碼保護的 DC 代理程式服務收到密碼篩選 DLL 的 DC 代理程式的密碼驗證要求。 它使用目前的 （在本機提供） 的密碼原則就可以將它處理它們，並傳回結果：*傳遞*或是*失敗*。

## <a name="how-password-protection-works"></a>密碼保護的運作方式

每個 Azure AD 密碼保護 Proxy 服務執行個體通告本身的網域控制站，樹系中建立**serviceConnectionPoint** Active Directory 中的物件。

密碼保護的每個 DC 代理程式服務也會建立**serviceConnectionPoint** Active Directory 中的物件。 此物件主要用於報告和診斷。

DC 代理程式服務負責初始化下載新的密碼原則，從 Azure AD。 第一個步驟是找出 Azure AD 密碼保護 Proxy 服務藉由查詢 proxy 的樹系**serviceConnectionPoint**物件。 找到可用的 proxy 服務時，DC 代理程式會傳送至 proxy 服務的密碼原則下載要求。 Proxy 服務接著會將要求傳送至 Azure AD。 Proxy 服務，然後傳回 DC 代理程式服務的回應。

DC 代理程式服務從 Azure AD 收到新的密碼原則之後，服務會將原則儲存在專用的資料夾，在其網域的根目錄*sysvol*資料夾共用。 如果較新的原則中，複寫從網域中的其他 DC 代理程式服務的 DC 代理程式服務也會監視此資料夾。

此 DC 代理程式服務一律會要求在服務啟動新的原則。 DC 代理程式服務啟動之後，它會檢查每小時的目前在本機上可用的原則存在時間。 如果原則超過一小時，DC 代理程式要求新的原則從 Azure AD 中，如先前所述。 如果目前的原則不超過一小時時，DC 代理程式會繼續使用該原則。

下載 Azure AD 密碼保護的密碼原則之後，每當該原則是特定租用戶。 換句話說，密碼原則一律是 Microsoft 全域的禁用密碼清單和每一租用戶自訂的禁用密碼清單的組合。

DC 代理程式會透過 TCP 通訊透過 RPC proxy 服務。 Proxy 服務會接聽動態或靜態 RPC 連接埠，視設定而定，這些呼叫。

DC 代理程式永遠不會接聽網路提供的連接埠。

Proxy 服務永遠不會呼叫 DC 代理程式服務。

Proxy 服務是無狀態。 它永遠不會快取原則，或從 Azure 下載的任何其他狀態。

DC 代理程式服務一律使用最新的本機可用密碼原則，以評估使用者的密碼。 是否可在本機網域控制站沒有密碼原則，自動接受的密碼。 當發生這種情況時，則事件訊息會記錄警告系統管理員。

Azure AD 密碼保護並非即時原則應用程式引擎。 可以在 Azure AD 中進行的密碼原則設定變更時，與當變更到達，會強制執行所有網域控制站之間的延遲。

## <a name="foresttenant-binding-for-password-protection"></a>樹系/租用戶密碼保護的繫結

Active Directory 樹系中的 Azure AD 密碼保護的部署需要該樹系與 Azure AD 註冊。 部署每個 proxy 服務也必須向 Azure AD。 這些樹系和 proxy 註冊相關聯的特定 Azure AD 租用戶，以在註冊期間所使用的認證會隱含地識別。

Active Directory 樹系和樹系內的所有已部署的 proxy 服務都必須向相同的租用戶。 它不支援將 Active Directory 樹系或任何 proxy 服務，因為樹系註冊至不同的 Azure AD 租用戶。 這類未正確設定的部署的徵兆包括無法下載的密碼原則。

## <a name="download"></a>下載

Azure AD 密碼保護的兩個必要的代理程式安裝程式都是從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)。

## <a name="next-steps"></a>後續步驟
[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
