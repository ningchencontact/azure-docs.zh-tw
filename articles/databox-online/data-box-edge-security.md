---
title: Azure 資料方塊的邊緣安全性 |Microsoft Docs
description: 描述保護您的 Azure 資料方塊的邊緣裝置、 服務和內部部署和雲端中的資料安全性和隱私權功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682095"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure 資料方塊的邊緣安全性和資料保護

安全性是主要考量時採用新技術，特別是當技術用於機密或專屬資料。 Microsoft Azure 資料方塊邊緣解決方案可協助確保只有已獲授權的實體，可以檢視、 修改或刪除您的資料。

本文說明可協助保護每個解決方案的元件和在其上儲存的資料的資料方塊的邊緣安全性功能。

Azure 資料方塊邊緣解決方案是由四個彼此互動的主要元件所組成：

- **在 Azure 中裝載的資料方塊邊緣服務**– 管理資源供您建立的裝置順序，設定裝置，然後追蹤到完成的順序。
- **資料方塊邊緣裝置**– 寄送到您內部部署資料匯入 Azure 傳輸裝置。
- **用戶端/主機連線到裝置**– 基礎結構中的用戶端，連接到資料方塊的邊緣裝置，並包含要保護的資料。
- **雲端儲存體** – Azure 雲端中儲存資料的位置。 這個位置通常是儲存體帳戶連結到您所建立之資料方塊邊緣資源。

## <a name="data-box-edge-service-protection"></a>方塊邊緣服務保護資料

資料方塊邊緣的服務是裝載於 Microsoft Azure 管理服務。 服務用來設定和管理裝置。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>資料方塊邊緣裝置保護

資料方塊的邊緣裝置是在內部部署裝置，可協助您在本機處理它，然後將它傳送至 Azure 來轉換資料。 您的裝置：

- 必須啟用金鑰來存取資料方塊邊緣的服務。
- 在所有時間裝置密碼保護。
- 是鎖定的裝置。 裝置 BMC 和 BIOS 是用於將 BIO 的限制使用者存取受密碼保護。
- 已啟用安全開機。
- 執行 Windows Defender Device Guard。 Device Guard 可讓您執行只受信任的應用程式，您在程式碼完整性原則中定義。

### <a name="protect-the-device-via-activation-key"></a>保護裝置，透過啟用金鑰

只有已獲授權的資料方塊的邊緣裝置允許加入您在您的 Azure 訂用帳戶中建立的資料方塊邊緣服務。 若要授權裝置，您必須使用啟用金鑰啟用裝置與資料方塊邊緣的服務。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

如需詳細資訊，請移至[取得啟用金鑰](data-box-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>保護透過密碼的裝置

密碼會確保您的資料是只有授權使用者存取。 資料方塊邊緣裝置開機為鎖定狀態。

您可以：

- 連接到本機 web UI，透過瀏覽器的裝置，然後提供 使用密碼來登入裝置。
- 遠端連線到裝置的 PowerShell 介面透過 HTTP。 依預設會開啟遠端管理。 然後，您可以提供裝置密碼來登入裝置。 如需詳細資訊，請移至[從遠端連接至您的資料方塊的邊緣裝置](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本機 web UI 來[變更密碼](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果您變更密碼，請務必通知所有遠端存取使用者，讓它們不會遇到登入失敗。

## <a name="protect-the-data"></a>保護資料

本節說明保護傳輸中的資料和預存的資料的資料方塊的邊緣安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保護傳輸中的資料

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>透過儲存體帳戶保護資料安全

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 旋轉，然後[同步處理儲存體帳戶金鑰](data-box-edge-manage-shares.md#sync-storage-keys)定期以協助確保未經授權的使用者無法存取您的儲存體帳戶。

## <a name="manage-personal-information"></a>管理個人資訊

資料方塊邊緣的服務會收集下列索引鍵的執行個體中的個人資訊：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要檢視的使用者可以存取或刪除共用的清單，請遵循[管理資料方塊邊緣上的共用](data-box-edge-manage-shares.md)。

如需詳細資訊，請在[信任中心](https://www.microsoft.com/trustcenter)檢閱 Microsoft 隱私權原則。

## <a name="next-steps"></a>後續步驟

[部署您的資料方塊的邊緣裝置](data-box-edge-deploy-prep.md)。
