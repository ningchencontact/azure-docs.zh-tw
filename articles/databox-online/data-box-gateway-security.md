---
title: Azure 中部署資料閘道的安全性 |Microsoft Docs
description: 描述安全性和隱私權功能，保護 Azure 中部署資料閘道的虛擬裝置、 服務和資料，在內部部署和雲端中。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754265"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 中部署資料閘道的安全性和資料保護

安全性是主要考量時您採用新技術，特別是當技術用於機密或專屬資料。 Azure 中部署資料閘道可協助您確保只有獲得授權的實體都可以檢視、 修改或刪除您的資料。

本文說明 Azure 中部署資料閘道的安全性功能，協助您保護每個解決方案的元件和其中儲存的資料。

方塊部署資料閘道解決方案是由四個彼此互動的主要元件所組成：

- **在 Azure 中裝載的資料 方塊中的閘道服務**。 設定裝置，您用來建立裝置順序，將管理資源，然後追蹤到完成的順序。
- **資料方塊閘道裝置**。 您的系統，您所提供的 hypervisor 中佈建虛擬裝置。 此虛擬裝置用來在內部部署資料匯入 Azure。
- **用戶端/主機連線到裝置**。 用戶端基礎結構中，連接到資料方塊閘道裝置，而且包含需要保護的資料。
- **雲端儲存體**。 在 Azure 雲端平台儲存資料的位置。 這個位置通常是儲存體帳戶連結到您所建立之方塊部署資料閘道資源。


## <a name="data-box-gateway-service-protection"></a>保護資料 方塊中的閘道服務

管理服務裝載於 Azure 中部署資料閘道服務。 服務用來設定和管理裝置。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>資料閘道 方塊中的裝置保護

資料方塊閘道裝置會在您提供內部部署系統的 hypervisor 中佈建虛擬裝置。 裝置可協助將資料傳送至 Azure。 您的裝置：

- 必須啟用金鑰來存取資料方塊邊緣/資料方塊閘道服務。
- 在所有時間裝置密碼保護。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>保護裝置，透過啟用金鑰

只有已獲授權的資料方塊閘道裝置可以加入您在您的 Azure 訂用帳戶中建立的方塊部署資料閘道服務。 若要授權裝置，您需要使用啟用金鑰啟用方塊部署資料閘道服務的裝置。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

如需詳細資訊，請參閱 <<c0> [ 取得啟用金鑰](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>保護透過密碼的裝置

密碼會確保只有授權的使用者可以存取您的資料。 資料閘道 方塊中的裝置開機為鎖定狀態。

您可以：

- 連接到本機 web UI，透過瀏覽器的裝置，然後提供 登入裝置的密碼。
- 從遠端透過 HTTP 連線至裝置的 PowerShell 介面。 依預設會開啟遠端管理。 然後，您可以提供裝置密碼來登入裝置。 如需詳細資訊，請參閱 <<c0> [ 從遠端連接至您的資料方塊閘道裝置](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本機 web UI 來[變更密碼](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果您變更密碼，請務必通知所有遠端存取使用者，讓他們不需要登入的問題。


## <a name="protect-your-data"></a>保護您的資料

本節說明保護傳輸中和預存資料的方塊部署資料閘道的安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保護傳輸中的資料

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 旋轉，然後[同步處理儲存體帳戶金鑰](data-box-gateway-manage-shares.md#sync-storage-keys)定期以協助防止未經授權的使用者的儲存體帳戶。

## <a name="manage-personal-information"></a>管理個人資訊

方塊部署資料閘道服務會收集個人資訊，在下列情況：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要檢視的使用者可以存取或刪除共用的清單，請遵循[管理資料方塊閘道上的共用](data-box-gateway-manage-shares.md)。

如需詳細資訊，請檢閱 Microsoft 隱私權原則上[信任中心](https://www.microsoft.com/trustcenter)。

## <a name="next-steps"></a>後續步驟

[部署您的資料方塊閘道裝置](data-box-gateway-deploy-prep.md)
