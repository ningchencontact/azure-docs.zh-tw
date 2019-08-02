---
title: Azure 澳大利亞的資料安全性
description: 在澳大利亞地區內設定 Azure, 以符合澳大利亞政府原則、法規及法規的特定需求。
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608343"
---
# <a name="data-security-in-azure-australia"></a>Azure 澳大利亞的資料安全性

保護客戶資料的首要原則是︰

* 使用加密保護資料
* 管理密碼
* 限制資料存取

## <a name="encrypting-your-data"></a>加密您的資料

資料的加密可以在磁片層級 (待用和傳輸中)、應用程式中 (傳輸中), 以及網路 (傳輸中) 中進行的方式套用。 有幾種方式可以在 Azure 中達到加密:

|服務/功能|描述|
|---|---|
|儲存體服務加密|Azure 儲存體服務加密是在儲存體帳戶層級啟用，讓區塊 Blob 和分頁 Blob 可以在寫入 Azure 儲存體時自動加密。 當您從 Azure 儲存體讀取資料時，儲存體服務會在傳回資料之前將之解密。 使用 SSE 來保護您的資料, 而不需要修改或將程式碼新增至任何應用程式。|
|Azure 磁碟加密|使用 Azure 磁碟加密可加密 Azure 虛擬機器所使用的作業系統磁碟和資料磁碟。 與 Azure 金鑰保存庫整合可給予您控制權，並協助您管理磁碟加密金鑰。|
|用戶端應用層級加密|用戶端加密內建于 JAVA 和 .NET 儲存體用戶端程式庫, 可以利用 Azure Key Vault Api, 讓它能夠直接執行。 使用 Azure Key Vault, 以使用 Azure Active Directory 存取特定個人 Azure Key Vault 中的秘密。|
|傳輸中加密|適用于 Azure 澳大利亞連線的基本加密支援傳輸層級安全性 (TLS) 1.2 通訊協定和 x.509 憑證。 聯邦資訊處理標準 (FIPS) 140-2 層級1密碼編譯演算法也會用於 Azure 澳大利亞資料中心之間的基礎結構網路連線。  Windows Server 2016、Windows 10、Windows Server 2012 R2、Windows 8.1 和 Azure 檔案共用可以使用 SMB 3.0 進行 VM 和檔案共用之間的加密。 使用用戶端加密來加密資料, 然後再將其傳輸至用戶端應用程式中的儲存體, 以及在資料從儲存體傳出後進行解密。|
|IaaS Vm|使用 Azure 磁碟加密。 開啟儲存體服務加密，來加密在 Azure 儲存體中用來備份這些磁碟的 VHD 檔案，但它只會加密新寫入的資料。 這表示，如果您建立 VM，然後在保留 VHD 檔案的儲存體帳戶上啟用儲存體服務加密，則只會加密變更，而不會加密原始的 VHD 檔案。|
|用戶端加密|這是加密資料的最安全方法, 因為它會在傳輸前加密, 並加密待用資料。 不過，它需要您使用儲存體將程式碼加入應用程式，而您可能不想這樣做。 在這些情況下, 您可以針對傳輸中的資料使用 HTTPS, 並儲存體服務加密來加密待用資料。 用戶端加密也牽涉到更多用戶端負載, 您必須在您的擴充性計畫中考慮這一點, 特別是當您要加密和傳輸大量資料時。|
|

如需 Azure 中加密選項的詳細資訊, 請參閱[儲存體安全性指南](https://docs.microsoft.com/azure/storage/storage-security-guide)。

## <a name="protecting-data-by-managing-secrets"></a>藉由管理秘密來保護資料

安全金鑰管理對於雲端保護資料十分重要。 客戶應努力簡化金鑰管理，並持續掌控雲端應用程式和服務用來加密資料的金鑰。

### <a name="managing-secrets"></a>管理密碼

* 使用金鑰保存庫將可能透過硬式編碼組態檔、指令碼或原始程式碼公開密碼的風險降到最低。 Azure 金鑰保存庫會加密金鑰 (例如 Azure 磁碟加密的加密金鑰) 和機密資料 (例如密碼)，方法是將它們儲存在通過 FIPS 140-2 Level 2 驗證的硬體安全性模組 (HSM) 中。 為了加強保證，您可以在這些 HSM 中匯入或產生金鑰。
* 應用程式代碼和範本應該只包含秘密的 URI 參考 (這表示實際的密碼不在程式碼、設定或原始程式碼存放庫中)。 這可防止內部或外部儲存機制的金鑰網路釣魚攻擊，例如 GitHub 中的 harvest-bots。
* 在金鑰保存庫內使用強式 RBAC 控制。 如果受信任的操作員離職或轉調到公司內的新群組，則應該防止讓他們能夠存取機密資料。  

如需詳細資訊, 請參閱[Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>隔離以限制資料存取

隔離的關鍵在於使用界限、分割和容器，以限制只有經過授權的使用者、服務和應用程式能夠存取資料。 例如, 租使用者之間的區隔是多租使用者雲端平臺的基本安全性機制, 例如 Microsoft Azure。 邏輯隔離有助於防止租用戶干擾任何其他租用戶的作業。

#### <a name="per-customer-isolation"></a>每一客戶隔離

Azure 會透過第2層 VLAN 隔離、存取控制清單、負載平衡器和 IP 篩選器, 來實行網路存取控制和隔離。

客戶可進一步隔離其在訂用帳戶、資源群組、虛擬網路和子網路的資源。

如需 Microsoft Azure 中隔離的詳細資訊, 請參閱[Azure 公用雲端中的隔離](../security/fundamentals/isolation-choices.md)。

## <a name="next-steps"></a>後續步驟

請參閱[Azure VPN 閘道](vpn-gateway.md)上的文章