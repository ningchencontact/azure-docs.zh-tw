---
title: Azure AD Connect 健康情況 - 健康情況服務的資料並非最新警示 | Microsoft Docs
description: 本文說明「健康情況服務的資料並非最新」警示的成因，以及如何進行疑難排解。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 3f97e9696a5138d9102037103a45c86988a7506c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489270"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>健康情況服務的資料並非最新警示

## <a name="overview"></a>概觀
<li>當 Azure AD Connect Health 在兩小時內未從伺服器收到所有資料點，就會產生資料更新警示。 此警示的標題為**健康情況服務的資料並非最新**。 </li>
<li>如果 Connect Health 在兩小時內未接收到從伺服器傳送的部分資料元素，就會引發 [警告] 狀態警示。 警告狀態警示不會觸發傳送給租用戶管理員的電子郵件通知。 </li>
<li>如果 Connect Health 在兩小時內未接收到從伺服器傳送的任何資料元素，就會引發 [錯誤] 狀態警示。 錯誤狀態警示會觸發傳送給租用戶管理員的電子郵件通知。 </li>

>[!IMPORTANT] 
> 此警示會遵循 Connect Health 的[資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>疑難排解步驟 
* 確實詳讀[需求小節](how-to-connect-health-agent-install.md#requirements)，並符合相關要求。
* 使用[測試連線工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)找出連線問題。
* 如果您有 HTTP Proxy，請遵循[這裡的設定步驟](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。 

### <a name="connect-health-for-sync"></a>適用於同步處理的 Connect Health

| 資料元素 | 疑難排解步驟 |
| --- | --- | 
| PerfCounter | - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已篩選或停用輸出流量的 SSL 檢查](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [如果啟用 IE 增強式安全性，則允許指定的網站](https://technet.microsoft.com/windows/ms537180(v=vs.60)) |
| AadSyncService-SynchronizationRules， <br /> AadSyncService-Connectors， <br /> AadSyncService-GlobalConfigurations， <br /> AadSyncService-RunProfileResults， <br /> AadSyncService-ServiceConfigurations， <br /> AadSyncService-ServiceStatus | - 以 IP 位址為基礎的輸出連線，請參閱 [Azure IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>適用於 ADFS 的 Connect Health

執行額外步驟以驗證 AD FS，並且遵循 [AD FS 說明](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)中的工作流程。

| 資料元素 | 疑難排解步驟 |
| --- | --- | 
| PerfCounter，TestResult | - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已篩選或停用輸出流量的 SSL 檢查](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [如果啟用 IE 增強式安全性，則允許指定的網站](https://technet.microsoft.com/windows/ms537180(v=vs.60)) |
|  Adfs-UsageMetrics | 以 IP 位址為基礎的輸出連線，請參閱 [Azure IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>適用於 ADDS 的 Connect Health

| 資料元素 | 疑難排解步驟 |
| --- | --- | 
| PerfCounter，Adds-TopologyInfo-Json，Common-TestData-Json | - [Azure 服務端點的輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [已篩選或停用輸出流量的 SSL 檢查](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [防火牆連接埠 (位於執行代理程式的伺服器上)](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [如果啟用 IE 增強式安全性，則允許指定的網站](https://technet.microsoft.com/windows/ms537180(v=vs.60)) <br />  - 以 IP 位址為基礎的輸出連線，請參閱 [Azure IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)  |


## <a name="next-steps"></a>後續步驟
* [Azure AD Connect Health 常見問題集](reference-connect-health-faq.md)
