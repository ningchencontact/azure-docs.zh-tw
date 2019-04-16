---
title: 方塊邊緣的資料安全性 |Microsoft Docs
description: 描述保護您的資料方塊的邊緣裝置、 服務和內部部署和雲端中的資料安全性和隱私權功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 197c2c021dd9f674d196fb1169155bc6015f2e79
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578605"
---
# <a name="data-box-edge-security-and-data-protection"></a>方塊邊緣的資料安全性和資料保護

安全性是主要考量時採用新技術，特別是當技術用於機密或專屬資料。 Microsoft Azure 資料方塊邊緣解決方案可協助確保只有已獲授權的實體，可以檢視、 修改或刪除您的資料。

本文說明可協助保護每個解決方案的元件和在其上儲存的資料的資料方塊的邊緣安全性功能。

Azure 資料方塊邊緣解決方案是由四個彼此互動的主要元件所組成：

- **資料方塊邊緣 / 方塊部署資料閘道服務裝載於 Azure** – 管理資源供您建立的裝置順序，設定裝置，然後追蹤到完成的順序。
- **資料方塊邊緣裝置**– 寄送到您內部部署資料匯入 Azure 傳輸裝置。
- **用戶端/主機連線到裝置**– 基礎結構中的用戶端，連接到資料方塊的邊緣裝置，並包含要保護的資料。
- **雲端儲存體** – Azure 雲端中儲存資料的位置。 這個位置通常是儲存體帳戶連結到您所建立之資料方塊邊緣資源。


## <a name="data-box-edgedata-box-gateway-service-protection"></a>方塊邊緣/資料方塊閘道服務保護資料

資料方塊邊緣/資料方塊的閘道器服務是裝載於 Microsoft Azure 管理服務。 服務用來設定和管理裝置。

- 資料方塊邊緣/資料方塊閘道服務的存取需要您的組織有 Enterprise 合約 (EA) 或雲端解決方案提供者 (CSP) 訂用帳戶。 如需詳細資訊，請移至[註冊 Azure 訂用帳戶](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)！
- 在 Azure 中裝載您的管理服務，因為它會受到 Azure 安全性功能。 如需有關 Microsoft Azure 所提供的安全性功能的詳細資訊，請移至 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/)。
- SDK 的管理作業的加密金鑰是供您的資料 方塊邊緣 / 方塊部署資料閘道資源，在底下**裝置屬性**。 只有當您擁有資源的 Graph api 的權限，您可以檢視加密金鑰。

## <a name="data-box-edge-device-protection"></a>資料方塊邊緣裝置保護

資料方塊的邊緣裝置是在內部部署裝置，可協助您在本機處理它，然後將它傳送至 Azure 來轉換資料。 您的裝置：

- 必須啟用金鑰來存取資料方塊邊緣/資料方塊閘道服務。
- 在所有時間裝置密碼保護。
- 是鎖定的裝置。 裝置 BMC 和 BIOS 是用於將 BIO 的限制使用者存取受密碼保護。
- 已啟用安全開機。
- 執行 Windows Defender Device Guard。 Device Guard 可讓您執行只受信任的應用程式，您在程式碼完整性原則中定義。
- 有可以用來鎖定裝置前蓋內索引鍵。 我們建議您設定裝置之後, 開啟封面。 找出機碼，並再鎖定以防止未經授權的存取位於裝置前面的資料磁碟封面。

### <a name="protect-the-device-via-activation-key"></a>保護裝置，透過啟用金鑰

只有已獲授權的資料方塊的邊緣裝置允許加入您在您的 Azure 訂用帳戶中建立的資料方塊邊緣/資料方塊閘道服務。 若要授權裝置，您必須使用啟用金鑰啟用資料方塊邊緣/資料方塊閘道服務的裝置。 如需詳細資訊，請移至[取得啟用金鑰](data-box-edge-deploy-prep.md#get-the-activation-key)。

您使用的啟用金鑰：

- 是一種 Azure Active Directory (AAD) 型的驗證金鑰。
- 在三天後到期。
- 不會使用裝置啟動之後。
 
裝置啟動之後，它會使用權杖與 Microsoft Azure 通訊。

### <a name="protect-the-device-via-password"></a>保護透過密碼的裝置

密碼會確保您的資料是只有授權使用者存取。 資料方塊邊緣和資料方塊閘道裝置開機為鎖定狀態。

您可以：

- 連接到本機 web UI，透過瀏覽器的裝置，然後提供 使用密碼來登入裝置。
- 遠端連線到裝置的 PowerShell 介面透過 HTTP。 依預設會開啟遠端管理。 然後，您可以提供裝置密碼來登入裝置。 如需詳細資訊，請移至[從遠端連接至您的資料方塊的邊緣裝置](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

請記住下列最佳做法：

- 資料方塊邊緣的服務無法擷取現有的密碼： 它只能重設他們的 Azure 入口網站。 建議您將所有密碼儲存在安全的地方，讓您在忘記密碼時無需重設密碼。 如果您重設密碼，請務必通知所有使用者，然後您將它重設。
- 使用本機 web UI 來[變更密碼](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果您變更密碼，請務必通知所有遠端存取使用者，讓它們不會遇到登入失敗。
- 您可以透過 HTTP 的遠端存取您的裝置的 Windows PowerShell 介面。 最佳安全性做法，您應該只在受信任的網路上使用 HTTP。
- 請確定裝置密碼強式且受到妥善保護。 請遵循[密碼的最佳作法](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management)。

## <a name="protect-the-data"></a>保護資料

本節說明保護傳輸中的資料和預存的資料的資料方塊的邊緣安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

針對資料待用：

- 資料待用，資料方塊邊緣會使用 BitLocker XTS AES-256 加密保護的本機資料。
- 位於 共用資料的存取權的共用時受到限制。

    - 存取共用資料的 SMB 用戶端，它們需要與共用相關聯的使用者認證。 這些認證會在共用建立時定義。
    - 存取共用的 NFS 用戶端，用戶端的 IP 位址需要在建立共用時加入。


### <a name="protect-data-in-flight"></a>保護傳輸中的資料

資料層中-班機：

- 會在裝置與 Azure 之間的資料，會使用標準的 TLS 1.2。 沒有任何後援至 TLS 1.1 或更早版本。 不支援 TLS 1.2，則為，將會封鎖代理程式通訊。 TLS 1.2 時也需要針對入口網站與 SDK 管理作業。
- 當用戶端會存取您的裝置，透過本機 web UI，在瀏覽器中時，標準的 TLS 1.2 作為預設的安全通訊協定。

    - 最佳做法是設定您的瀏覽器使用 TLS 1.2。
    - 如果瀏覽器不支援 TLS 1.2，您可以使用 TLS 1.1 或 TLS 1.0。
- 若要保護的資料，當您從您資料的伺服器進行複製時，我們建議，您會使用 SMB 3.0 加密。

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

您的裝置已為您在 Azure 中的資料當做目的地儲存體帳戶相關聯。 儲存體帳戶的存取權由訂用帳戶和兩個 512 位元儲存體控制存取該儲存體帳戶相關聯的金鑰。

資料方塊的邊緣裝置來存取儲存體帳戶時，其中一個金鑰用於驗證。 其他金鑰會被保留，讓您可以定期輪替金鑰。

基於安全性理由，許多資料中心需要金鑰輪替。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 謹慎保護您的帳戶金鑰。 不要散發給其他使用者的密碼、 硬式程式碼，或將它儲存在其他人可以存取的純文字。
- [重新產生帳戶金鑰](../storage/common/storage-account-manage.md#regenerate-access-keys)使用 Azure 入口網站中，如果您認為它可能已遭入侵。
- 旋轉，然後[同步處理儲存體帳戶金鑰](data-box-gateway-manage-shares.md#sync-storage-keys)定期以協助確保未經授權的使用者無法存取您的儲存體帳戶。
- Azure 系統管理員應該使用 Azure 入口網站的 [儲存體] 區段來直接存取儲存體帳戶，以定期變更或重新產生主要或次要金鑰。


## <a name="manage-personal-information"></a>管理個人資訊

資料箱 Edge / 方塊部署資料閘道服務會收集下列索引鍵的執行個體中的個人資訊：

- **訂單詳細資料** – 一旦訂單建立後，交貨地址、電子郵件、使用者的連絡人資訊都會儲存在 Azure 入口網站中。 儲存的資訊包括︰
  - 連絡人姓名
  - 電話號碼
  - 電子郵件
  - 街道地址
  - City
  - 郵遞區號
  - State
  - 國家/地區/州/省/區域
  - 運送追蹤號碼

    訂單詳細資料會經過加密及儲存在服務中。 除非您明確地刪除資源或順序，服務就會保留這些資訊。 此外，刪除資源和對應的訂單會封鎖該裝置隨附的裝置傳回給 Microsoft 之前的時間。

- **交貨地址**– 資料箱服務訂單之後，提供第三方電訊廠商，例如 UPS 送貨地址。

- **共用使用者**-在您的裝置上的使用者也可以存取位於共用上的資料。 顯示可存取共用資料之使用者的清單，並且可檢視。 刪除共用時，也會刪除這份清單。 若要檢視的使用者可以存取或刪除共用的清單，請遵循[管理資料方塊邊緣上的共用](data-box-gateway-manage-shares.md)。

如需詳細資訊，請在[信任中心](https://www.microsoft.com/trustcenter)檢閱 Microsoft 隱私權原則。

## <a name="next-steps"></a>後續步驟

[部署您的資料方塊的邊緣裝置](data-box-edge-deploy-prep.md)。

