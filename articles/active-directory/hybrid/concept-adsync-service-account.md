---
title: Azure AD Connect：ADSync 服務帳戶 |Microsoft Docs
description: 本主題描述 ADSync 服務帳戶，並提供關於帳戶的最佳作法。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478715"
---
# <a name="adsync-service-account"></a>ADSync 服務帳戶
Azure AD Connect 會安裝在內部部署服務可協調 Active Directory 與 Azure Active Directory 之間的同步處理。  Microsoft Azure AD 同步的同步處理服務 (ADSync) 會在內部部署環境中的伺服器上執行。  服務的認證都使用 Express 安裝中的預設設定，但您可以自訂以符合組織安全性需求。  這些認證不會用來連接到您的內部部署樹系或 Azure Active Directory。

選擇 ADSync 服務帳戶是製作，再安裝 Azure AD Connect 的重要規劃決策。  任何嘗試變更的認證之後安裝將會導致服務無法啟動，, 失去存取權的同步處理資料庫，並無法使用您已連線的目錄 （Azure 和 AD DS） 進行驗證。  直到還原原始的認證，就會不發生任何同步處理。

## <a name="the-default-adsync-service-account"></a>預設 ADSync 服務帳戶

成員伺服器上執行時，AdSync 服務執行中的虛擬服務帳戶 (VSA) 的內容。  由於產品的限制，網域控制站上安裝時，會建立自訂的服務帳戶。  如果快速設定服務帳戶不符合您的組織安全性需求，選擇 [自訂] 選項來部署 Azure AD Connect。  然後選擇符合您的組織需求的服務帳戶選項。

>[!NOTE]
>表單 Domain\AAD_InstallationIdentifier 是安裝在網域控制站上時，才會進行預設服務帳戶。  此帳戶的密碼隨機產生，並提供修復與密碼輪替所遭遇的重大挑戰。  Microsoft 建議使用獨立或群組 「 受管理的服務帳戶的網域控制站上進行初始安裝期間自訂的服務帳戶 (sMSA / gMSA)

|Azure AD Connect 的位置|建立服務帳戶|
|-----|-----|
|成員伺服器|NT SERVICE\ADSync|
|網域控制站|Domain\AAD_74dc30c01e80 (see note)|

## <a name="custom-adsync-service-accounts"></a>自訂的 ADSync 服務帳戶
Microsoft 建議您執行 ADSync 服務的虛擬服務帳戶或獨立的內容中，或群組受控服務帳戶。  您的網域系統管理員也可以選擇建立佈建到符合您特定組織的安全性需求的服務帳戶。   若要自訂安裝期間使用的服務帳戶，請選擇下方的 [快速設定] 頁面上的 [自訂] 選項。   有下列選項可供使用：

- 預設帳戶，Azure AD Connect 會佈建的服務帳戶，如上面所述
- 受控服務帳戶 – 使用獨立或群組 MSA 佈建您的系統管理員
- 網域帳戶，使用網域服務佈建您的系統管理員的帳戶

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>診斷 ADSync 服務帳戶變更
之後安裝將會導致服務無法啟動，失去存取權的同步處理資料庫，並無法與已連線目錄 （Azure 和 AD DS） 一起通過驗證，變更 ADSync 服務的認證。  新的 ADSync 服務帳戶授與資料庫存取權不足，無法從此問題中復原。 直到還原原始的認證，就會不發生任何同步處理。

ADSync 服務在無法啟動時，會發出錯誤層級訊息給事件記錄檔。  訊息的內容會因是否內建資料庫 (localdb) 或完整的 SQL 會在使用中。  以下是範例可能存在的事件記錄檔項目。

### <a name="example-1"></a>範例 1

找不到 AdSync 服務加密金鑰，並已重新建立。  在更正此問題之前，不會發生同步處理。

疑難排解此問題 Microsoft Azure AD Sync 加密金鑰將會變成無法存取 AdSync 服務登入認證變更。  如果認證已變更，使用服務應用程式登入帳戶變更回其原始設定的值 （例如。 NT SERVICE\AdSync) 重新啟動服務。  這會立即還原 AdSync 服務的正常的運作。

請參閱下列[文章](https://go.microsoft.com/fwlink/?linkid=2086764)如需詳細資訊。

### <a name="example-2"></a>範例 2

服務無法啟動，因為無法建立本機資料庫 (localdb) 的連接。

疑難排解此問題 Microsoft Azure AD 同步服務，將會遺失存取本機資料庫提供者，如果變更 AdSync 服務登入認證的權限。  如果認證已變更使用服務應用程式登入帳戶變更回其原始設定的值 （例如。 NT SERVICE\AdSync) 重新啟動服務。  這會立即還原 AdSync 服務的正常的運作。

請參閱下列[文章](https://go.microsoft.com/fwlink/?linkid=2086764)如需詳細資訊。

提供者已傳回下列錯誤訊息的其他詳細資料：
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。