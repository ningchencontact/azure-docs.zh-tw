---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原期間的組態伺服器問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 進行 VMware VM 和實體伺服器的災害復原，提供部署組態伺服器的疑難排解資訊。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/11/2018
ms.author: ramamill
ms.openlocfilehash: b819783d127c51c0d5f33b2273a37a4180cb13a6
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569735"
---
# <a name="troubleshoot-configuration-server-issues"></a>針對組態伺服器問題進行疑難排解

本文可協助您在部署及管理 [Azure Site Recovery](site-recovery-overview.md) 組態伺服器時排解疑難問題。 使用 Site Recovery 設定內部部署 VMware VM 和實體伺服器至 Azure 的災害復原時，會使用組態伺服器。 

## <a name="installation-failures"></a>安裝失敗

| **錯誤訊息** | **建議的動作** |
|--------------------------|------------------------|
|ERROR   無法載入帳戶。 錯誤︰System.IO.IOException︰無法在安裝和註冊 CS 伺服器時從傳輸連線讀取資料。| 確定電腦上已啟用 TLS 1.0。 |

## <a name="registration-failures"></a>註冊失敗

使用 %ProgramData%\ASRLogs 資料夾中的記錄，可以進行註冊失敗的偵錯。

檢閱 **%ProgramData%\ASRLogs** 資料夾中的記錄檔，即可進行註冊失敗的偵錯。

| **錯誤訊息** | **建議的動作** |
|--------------------------|------------------------|
|**09:20:06**：InnerException.Type: SrsRestApiClientLib.AcsException,InnerException。<br>訊息︰ACS50008：SAML 權杖無效。<br>追蹤識別碼︰1921ea5b-4723-4be7-8087-a75d3f9e1072<br>相互關聯識別碼︰62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>時間戳記︰**2016-12-12 14:50:08Z<br>** | 請確保系統時鐘上的時間與本地時間的偏差未超過 15 分鐘。 重新執行安裝程式以完成註冊。|
|**09:35:27**：嘗試取得所選憑證的所有災害復原保存庫時發生 DRRegistrationException：擲回 Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException，Exception.Message：ACS50008：SAML 權杖無效。<br>追蹤識別碼︰e5ad1af1-2d39-4970-8eef-096e325c9950<br>相互關聯識別碼︰abe9deb8-3e64-464d-8375-36db9816427a<br>時間戳記︰**2016-05-19 01:35:39Z**<br> | 請確保系統時鐘上的時間與本地時間的偏差未超過 15 分鐘。 重新執行安裝程式以完成註冊。|
|06:28:45︰無法建立憑證<br>06:28:45：安裝程式無法繼續。 無法建立驗證 Site Recovery 所需的憑證。 重新執行安裝程式 | 確定您是以本機系統管理員身分執行安裝程式。 |
