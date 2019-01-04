---
title: Azure Active Directory Domain Services：對網路安全性群組設定進行疑難排解 | Microsoft Docs
description: 針對 Azure AD Domain Services 的 NSG 設定進行疑難排解
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: 6e7d025e9e83f5511fce25d0c24e4da3b04d7e54
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957533"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>針對受控網域的無效網路設定進行移難排解
本文將協助您針對導致下列警示訊息的網路相關設定錯誤進行疑難排解：

## <a name="alert-aadds104-network-error"></a>警示 AADDS104：網路錯誤
**警示訊息：***Microsoft 無法觸達此受控網域的網域控制站。如果虛擬網路上設定的網路安全性群組 (NSG) 封鎖受控網域的存取，就可能發生這種情況。另一個可能的原因，是使用者定義的路由封鎖了來自網際網路的連入流量。*

Azure AD Domain Services 網路錯誤最常見的原因是 NSG 設定無效。 為您虛擬網路設定的「網路安全性群組」(NSG) 必須允許存取[特定連接埠](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)。 如果這些連接埠遭到封鎖，Microsoft 便無法監視或更新您的受控網域。 此外，也會影響到 Azure AD 目錄與受控網域之間的同步處理。 建立 NSG 時，請將這些連接埠保持開啟，以避免服務中斷。

### <a name="checking-your-nsg-for-compliance"></a>檢查 NSG 的合規性

1. 在 Azure 入口網站中，瀏覽至 [網路安全性群組](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 頁面
2. 從表格中，選擇與已啟用您受控網域的子網路關聯的 NSG。
3. 在左窗格的 [設定] 下，按一下 [輸入安全性規則]
4. 檢閱備妥的規則，並識別哪些規則會封鎖存取[這些連接埠](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. 對 NSG 進行以下編輯以確保合規性：刪除規則、新增規則，或建立全新的 NSG。 [新增規則](#add-a-rule-to-a-network-security-group-using-the-azure-portal)或[建立全新符合規範的 NSG](#create-a-nsg-for-azure-ad-domain-services-using-powershell) 的步驟如下

## <a name="sample-nsg"></a>NSG 範例
下表描述的 NSG 範例，能讓受控網域保持安全，同時允許 Microsoft 監視、管理及更新資訊。

![NSG 範例](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services 會要求來自虛擬網路的對外存取不受限制。 建議您不要建立任何額外的 NSG 規則來限制虛擬網路的對外存取。

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>使用 Azure 入口網站將規則新增至網路安全性群組
如果您不想使用 PowerShell，則可使用 Azure 入口網站，手動對 NSG 新增單一規則。 若要在網路安全性群組中建立規則，請完成下列步驟：

1. 在 Azure 入口網站中，瀏覽至 [網路安全性群組](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 頁面。
2. 從表格中，選擇與已啟用您受控網域的子網路關聯的 NSG。
3. 在左側面板的 [設定] 底下，按一下 [輸入安全性規則] 或 [輸出安全性規則]。
4. 按一下 [新增] 並填入資訊來建立規則。 按一下 [確定]。
5. 在規則資料表中尋找您的規則，確認規則已建立。


## <a name="need-help"></a>需要協助嗎？
請連絡 Azure Active Directory Domain Services 產品小組， [分享意見或尋求支援](active-directory-ds-contact-us.md)。
