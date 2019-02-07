---
title: Azure AD Connect 同步：變更 Azure AD Connect 同步服務帳戶 | Microsoft Docs
description: 本主題文件說明加密金鑰，以及如何在密碼變更後放棄它。
services: active-directory
keywords: Azure AD 同步處理服務帳戶, 密碼
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4c12675f28664da07aeebf8302b196d372c1f1d2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495406"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>變更 Azure AD Connect 同步處理服務帳戶密碼
如果您變更 Azure AD Connect 同步服務帳戶密碼，「同步處理服務」將會無法正確啟動，直到您放棄加密金鑰並將 Azure AD Connect 同步服務帳戶密碼重新初始化為止。 

Azure AD Connect 是同步處理服務的一部分，會使用加密金鑰來儲存 AD DS 與 Azure AD 服務帳戶的密碼。  這些帳戶會先加密再儲存到資料庫中。 

所使用的加密金鑰會使用 [Windows 資料保護 (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) 來提供保護。 DPAPI 為加密金鑰提供保護的方式是使用 **Azure AD Connect 同步處理服務帳戶的密碼**。 

如果您需要變更服務帳戶的密碼，您可以使用[放棄 Azure AD Connect 同步處理加密金鑰](#abandoning-the-azure-ad-connect-sync-encryption-key)中的程序來完成這項作業。  如果您基於任何原因而需要放棄加密金鑰，您也應該使用這些程序。

## <a name="issues-that-arise-from-changing-the-password"></a>變更密碼而引發的問題
當您在變更服務帳戶的密碼時，有兩件事必須完成。

第一件事，您必須在 Windows 服務控制管理員底下變更密碼。  在此問題獲得解決之前，您會看到下列錯誤︰


- 如果您嘗試在 Windows 服務控制管理員中啟動同步處理服務，您會收到錯誤「**Windows 無法在本機電腦上啟動 Microsoft Azure AD 同步處理服務**」。 **錯誤 1069:登入失敗所以服務無法啟動。**」
- 在 [Windows 事件檢視器] 底下，系統事件記錄包含**事件識別碼為 7038** 的錯誤和訊息 [ADSync 服務無法使用目前設定的密碼來登入，因為發生下列錯誤:使用者名稱或密碼不正確]。

第二件事，若密碼在特定狀況下做了更新，同步處理服務將無法再透過 DPAPI 擷取加密金鑰。 沒有加密金鑰，同步處理服務就無法將密碼解密，以供用來在內部部署 AD 和 Azure AD 進行同步處理。
您會看到如下錯誤︰

- 如果您嘗試在 Windows 服務控制管理員底下啟動同步處理服務，但此服務無法擷取加密金鑰，此啟動作業便會失敗，並出現錯誤 [Windows 無法在本機電腦上啟動 Microsoft Azure AD 同步。<strong>如需詳細資訊，請檢閱系統事件記錄。</strong>如果這是一項非 Microsoft 服務，請連絡該服務廠商，並參照服務特定錯誤碼 -21451857952]。
- Windows 事件檢視器底下的應用程式事件記錄包含**事件識別碼為 6028** 的錯誤和錯誤訊息「*無法存取伺服器加密金鑰*」。

若要確保不會收到這些錯誤，請在變更密碼時遵循[放棄 Azure AD Connect 同步處理加密金鑰](#abandoning-the-azure-ad-connect-sync-encryption-key)中的程序。
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>放棄 Azure AD Connect 同步處理加密金鑰
>[!IMPORTANT]
>下列程序只適用於 Azure AD Connect 組建 1.1.443.0 或更舊版本。

使用下列程序來放棄加密金鑰。

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>需要放棄加密金鑰時的作法

如果您需要放棄加密金鑰，請使用下列程序來完成這項作業。

1. [停止同步處理服務](#stop-the-synchronization-service)

1. [放棄現有的加密金鑰](#abandon-the-existing-encryption-key)

2. [提供 AD DS 帳戶的密碼](#provide-the-password-of-the-ad-ds-account)

3. [重新初始化 Azure AD 同步處理帳戶的密碼](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [啟動同步處理服務](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>停止同步處理服務
首先，您可以在 Windows 服務控制管理員中停止服務。  請確定在您嘗試停止服務時，服務未執行。  如果服務在執行中，請等候服務執行完成，再停止服務。


1. 移至 Windows 服務控制管理員 ([開始] → [服務])。
2. 選取 [Microsoft Azure AD 同步處理]，然後按一下 [停止]。

#### <a name="abandon-the-existing-encryption-key"></a>放棄現有的加密金鑰
放棄現有的加密金鑰，以便能夠建立新的加密金鑰︰

1. 以系統管理員身分登入您的 Azure AD Connect 伺服器。

2. 啟動新的 PowerShell 工作階段。

3. 瀏覽至資料夾：`$env:Program Files\Microsoft Azure AD Sync\bin\`

4. 執行命令：`./miiskmu.exe /a`

![Azure AD Connect 同步處理加密金鑰公用程式](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>提供 AD DS 帳戶的密碼
因為儲存在資料庫內的現有密碼無法再解密，您必須為同步處理服務提供 AD DS 帳戶的密碼。 同步處理服務會使用新的加密金鑰來將密碼加密︰

1. 啟動同步處理服務管理員 ([開始] → [同步處理服務])。
</br>![Sync Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. 移至 [連接器] 索引標籤。
3. 選取與內部部署 AD 對應的 [AD 連接器]。 如果您有多個 AD 連接器，請為每個連接器重複下列步驟。
4. 選取 [動作] 下方的 [屬性]。
5. 在快顯對話方塊中，選取 [連線至 Active Directory 樹系]：
6. 在 [密碼] 文字方塊中輸入 AD DS 帳戶的密碼。 如果您不知道該帳戶的密碼，您必須先將密碼設定為您知道的值，再執行此步驟。
7. 按一下 [確定] 以儲存新密碼，然後關閉快顯對話方塊。
![Azure AD Connect 同步處理加密金鑰公用程式](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>重新初始化 Azure AD 同步處理帳戶的密碼
您無法將 Azure AD 服務帳戶的密碼直接提供給同步處理服務。 相反地，您必須使用 **Add-ADSyncAADServiceAccount** Cmdlet 來重新初始化 Azure AD 服務帳戶。 此 Cmdlet 會重設帳戶密碼，並將密碼提供給同步處理服務︰

1. 在 Azure AD Connect 伺服器上啟動新的 PowerShell 工作階段。
2. 執行 `Add-ADSyncAADServiceAccount` Cmdlet。
3. 在快顯對話方塊中，提供 Azure AD 租用戶的 Azure AD 全域管理員認證。
![Azure AD Connect 同步處理加密金鑰公用程式](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. 如果成功，您會看到 PowerShell 命令提示字元。

#### <a name="start-the-synchronization-service"></a>啟動同步處理服務
由於同步處理服務已可存取所需要的加密金鑰和所有密碼，您可以在 Windows 服務控制管理員中重新啟動該服務︰


1. 移至 Windows 服務控制管理員 ([開始] → [服務])。
2. 選取 [Microsoft Azure AD 同步處理]，然後按一下 [重新啟動]。

## <a name="next-steps"></a>後續步驟
**概觀主題**

* [Azure AD Connect 同步：了解並自訂同步處理](how-to-connect-sync-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)
