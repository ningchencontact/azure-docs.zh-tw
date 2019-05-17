---
title: P2S 閘道的公開 CA 憑證轉換| Azure VPN 閘道 | Microsoft Docs
description: 本文可幫助您成功轉換到適用於 P2S 閘道的新公開 CA 憑證。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: cherylmc
ms.openlocfilehash: ea3ee00e60ae5eaff5c7220e9502da11358c8793
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786733"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>轉換為 P2S 的公用 CA 閘道憑證

Azure VPN 閘道不會再向其 P2S 連線閘道簽發 Azure 層級的自我簽署憑證。 已簽發的憑證現在由公開憑證授權單位 (CA) 簽署。 不過，某些較舊的閘道可能仍會使用自我簽署憑證。 這些自我簽署憑證接近其到期日，必須轉換為公開 CA 憑證。

>[!NOTE]
> * 此 Azure 層級的憑證變更不會影響用於驗證 P2S 用戶端的自我簽署憑證。 您可以如往常一般繼續簽發和使用自我簽署憑證。
>

此內容中的憑證是額外的 Azure 層級憑證。 這些憑證不是您在產生自有的自我簽署根憑證和用戶端憑證以供驗證時所使用的信任鏈結。 這些憑證仍不受影響，將會在您產生憑證時所設定的日期到期。

以前，閘道的自我簽署憑證 (由 Azure 在幕後簽發) 需要每 18 個月更新一次。 VPN 用戶端組態檔則必須產生，並重新部署到所有 P2S 用戶端。 移至公開 CA 憑證可消除此限制。 除了憑證轉換之外，此變更還提供了平台增強功能、更好的計量和更高的穩定性。

只有較舊的閘道才會受到此變更的影響。 如果需要轉換閘道憑證，您將在 Azure 入口網站中收到通訊或快顯通知。 您可以使用此文章中的步驟檢查您的閘道是否受到影響。

> [!IMPORTANT]
> **這項轉換已完成於 2019 5 月 12 日。**
>
> 轉換排定在 2019 年 3 月 12 日 UTC 時間 18:00 開始。 如果您喜歡不同的時間範圍，則可以建立支援案例。 至少提前 24 小時提出並完成您的要求。  您可以要求下列其中一個範圍：
>
> * 2 月 25 日 06:00 UTC
> * 2 月 25 日 18:00 UTC
> * 3 月 1 日 06:00 UTC
> * 3 月 1 日 18:00 UTC
>
> 所有其餘的閘道將於 2019 年 3 月 12 日 18:00 UTC 開始轉換。
>
> 客戶會在其閘道完成轉換程序時收到一封電子郵件。
> 

## <a name="1-verify-your-certificate"></a>1.驗證您的憑證

### <a name="resource-manager"></a>Resource Manager

1. 檢查您是否受到此更新的影響。 使用[此文章](point-to-site-vpn-client-configuration-azure-cert.md)中的步驟下載您目前的 VPN 用戶端設定。

2. 開啟或解壓縮 zip 檔案，並瀏覽至 [Generic] 資料夾。 在 [Generic] 資料夾中，您會看到兩個檔案，其中一個是 *VPNSettings.xml*。
3. 在任何 xml 檢視器/編輯器中開啟 *VPNSettings.xml*。 在 xml 檔案中，搜尋下列欄位：

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. 如果 *ServerCertRotCn* 和 *ServerCertIssuerCn* 是 "DigiCert Global Root CA"，則您不受此更新的影響，因此不需要繼續進行此文章中的步驟。 不過，如果它們顯示其他內容，則您的閘道憑證是更新的一部分，並將進行轉換。

### <a name="classic"></a>傳統

1. 用戶端電腦上，瀏覽至路徑`%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`。 在 [閘道識別碼] 資料夾中，您可以檢視憑證。
2. 在憑證的 [一般] 索引標籤上，確認簽發授權單位是 "DigiCert Global Root CA"。 如果您有此簽發授權單位以外的任何項目，則您的閘道憑證會是更新的一部分，而且將會進行轉換。

## <a name="2-check-certificate-transition-schedule"></a>2.檢查憑證轉換排程

如果您的憑證是更新的一部分，將會轉換您的閘道憑證。 有關轉換時間，請參閱**重要**附註。 更新後，P2S 用戶端將無法使用其舊的設定檔進行連線。 您必須產生新的 VPN 用戶端設定檔，並將其安裝在用戶端上。

## <a name="3-generate-vpn-client-configuration-profile"></a>3.產生 VPN 用戶端組態設定檔

憑證轉換後，請從 Azure 入口網站下載新的 VPN 設定檔 (VPN 用戶端組態檔)。 如需步驟，請參閱[建立並安裝 VPN 用戶端組態檔](point-to-site-vpn-client-configuration-azure-cert.md)。 您不需要產生新的用戶端憑證。

## <a name="4-deploy-vpn-client-profile"></a>4.部署 VPN 用戶端設定檔

將新的設定檔部署到所有點對站 VPN 用戶端。 在 VPN 設定檔下載並部署到用戶端裝置的新點對站連線之前，VPN 用戶端的連線會中斷。 已經安裝在 VPN 用戶端電腦上的用戶端憑證不需要重新簽發。

## <a name="5-connect-the-vpn-client"></a>5.連線 VPN 用戶端

像平常一樣從 VPN 用戶端連線到 Azure。
