---
title: Azure AD Connect 健康情況 - 健康情況服務的資料並非最新警示 | Microsoft Docs
description: 本文說明「健康情況服務的資料並非最新」警示的成因，以及如何進行疑難排解。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429005"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>健康情況服務的資料並非最新警示

## <a name="overview"></a>概觀
位於 Azure Active Directory Connect Health 監視的內部部署電腦上的代理程式，會定期將資料上傳到 Azure AD Connect Health 服務。 如果該服務沒有從代理程式接收到資料，入口網站中所顯示的資訊將會過時。 為了醒目提示該問題，服務將會引發 [健康情況服務資料不是最新的資料] 警示。 這會在服務於過去兩小時內皆沒有接收到資料的情況下產生。  

* 如果 Connect Health 在兩小時內未接收到從伺服器傳送的部分資料元素，就會引發 [警告] 狀態警示。 警告狀態警示不會觸發傳送給租用戶管理員的電子郵件通知。
* 如果 Connect Health 在兩小時內未接收到從伺服器傳送的任何資料元素，就會引發 [錯誤] 狀態警示。 錯誤狀態警示會觸發傳送給租用戶管理員的電子郵件通知。


## <a name="troubleshooting-steps"></a>疑難排解步驟 

> [!IMPORTANT] 
> 此警示會遵循 Connect Health 的[資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)

* 請確定 Azure AD Connect Health Agents 服務已在電腦上執行。 例如，適用於 AD FS 的 Connect Health 應該要有三個服務。  
  ![驗證 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 確實詳讀[需求小節](how-to-connect-health-agent-install.md#requirements)，並符合相關要求。
* 使用[測試連線工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)找出連線問題。
* 如果您有 HTTP Proxy，請遵循這些[設定步驟](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。 

[警示詳細資料] 刀鋒視窗會列出伺服器中遺失的資料元素。 下表將能協助進一步釐清問題的原因。 
### <a name="connect-health-for-sync"></a>適用於同步處理的 Connect Health

| 資料元素 | 疑難排解步驟 |
| --- | --- | 
| PerfCounter | - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已篩選或停用輸出流量的 SSL 檢查](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules， <br /> AadSyncService-Connectors， <br /> AadSyncService-GlobalConfigurations， <br /> AadSyncService-RunProfileResults， <br /> AadSyncService-ServiceConfigurations， <br /> AadSyncService-ServiceStatus | - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>適用於 ADFS 的 Connect Health

執行額外步驟以驗證 AD FS，並且遵循 [AD FS 說明](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)中的工作流程。

| 資料元素 | 疑難排解步驟 |
| --- | --- | 
| PerfCounter，TestResult | - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已篩選或停用輸出流量的 SSL 檢查](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | 以 IP 位址為基礎的輸出連線，請參閱 [Azure IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>適用於 ADDS 的 Connect Health

| 資料元素 | 疑難排解步驟 |
| --- | --- | 
| PerfCounter，Adds-TopologyInfo-Json，Common-TestData-Json | - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>後續步驟
* [Azure AD Connect Health 常見問題集](reference-connect-health-faq.md)
