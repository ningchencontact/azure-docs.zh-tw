---
title: 內部部署 Azure AD 密碼保護代理程式版本發行歷程記錄
description: 文件版本發行和行為變更歷程記錄
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: ccfe62e0002e3420303130840f1a0d393efb3420
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078758"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>預覽：Azure AD 密碼保護代理程式版本歷程記錄

|     |
| --- |
| Azure AD 密碼保護是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12250"></a>1.2.25.0

發行日期：2018/11/01

修正：

* DC 代理程式和 Proxy 服務應該不會再因為憑證信任失敗而失敗。
* DC 代理程式和 Proxy 服務有適用於 FIPS 合規電腦的其他修正程式。
* Proxy 服務現在將可在只有 TLS 1.2 的網路環境中正常運作。
* 次要的效能和穩健性修正
* 改進記錄功能

變更：

* 現在 Proxy 服務的最低必要作業系統層級是 Windows Server 2012 R2。 DC 代理程式服務的最低必要作業系統層級維持為 Windows Server 2012。
* 密碼驗證演算法採用擴充字元正規化資料表。 這可能會導致在舊版中接受的密碼遭到拒絕。

## <a name="12100"></a>1.2.10.0

發行日期：2018/8/17

修正：

* Register-AzureADPasswordProtectionProxy 與 Register-AzureADPasswordProtectionForest 現在支援多重要素驗證
* Register-AzureADPasswordProtectionProxy 需要網域中有 WS2012 或更新版本的網域控制站，避免發生加密錯誤。
* 啟動時向 Azure 要求新的密碼原則，使用 DC 代理程式服務較可靠。
* DC 代理程式服務會在必要時，每小時向 Azure 要求新的密碼原則，但現在能在隨機選取的開始時間進行。
* 在升階之前於伺服器上安裝為複本時，DC 代理程式服務不會再會導致新的 DC 公告無限期延遲。
* DC 代理程式服務現在允許 [啟用 Windows Server Active Directory 上的密碼保護] 組態設定
* 當升級至未來的版本時，DC 代理程式與 Proxy 安裝程式現在支援就地升級。

> [!WARNING]
> 不支援從 1.1.10.3 版就地升級，而且會造成安裝錯誤。 若要升級至 1.2.10 版或更新版本，您必須先將 DC 代理程式與 Proxy 服務軟體完全解決安裝，再從頭開始安裝新版本。 需要重新註冊 Azure AD 密碼保護 Proxy 服務。  不需要重新註冊樹系。

> [!NOTE]
> 就地升級 DC 代理程式軟體需要重新開機。

* DC 代理程式和 Proxy 服務現在支援在設定為僅使用 FIPS 相容演算法的伺服器上執行。
* 次要的效能和穩健性修正
* 改進記錄功能

## <a name="11103"></a>1.1.10.3

發行日期：2018/6/15

初始公開預覽版本

## <a name="next-steps"></a>後續步驟

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
