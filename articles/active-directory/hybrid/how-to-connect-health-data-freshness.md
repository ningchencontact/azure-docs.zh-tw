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
ms.openlocfilehash: 3ffd783ec41b1b0c4a11ee426648c1e36fbbbf75
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883695"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>健康情況服務的資料並非最新警示

## <a name="overview"></a>概觀

Azure AD Connect Health 會定期監視的內部部署機器上的代理程式會將資料上傳至 Azure AD Connect Health 服務。 如果服務不會收到來自代理程式的資料，入口網站會顯示的資訊將會過時。 若要反白顯示問題，服務將會引發**健全狀況服務資料不是最新狀態**警示。 當服務在過去兩個小時內未收到完整的資料時，會產生此警示。  

- **警告**狀態警示引發時，如果此健全狀況服務已收到只**部分**過去兩個小時內從伺服器傳送的資料類型。 警告狀態警示不會觸發設定的收件者的電子郵件通知。 
- **錯誤**如果健全狀況服務未收到任何資料類型的伺服器在過去兩小時，就會引發狀態警示。 錯誤狀態警示觸發程序來設定收件者的電子郵件通知。

服務取得的資料從內部部署機器，根據服務類型執行的代理程式。 下表列出在電腦、 它們的功用，和服務將會產生的資料類型執行的代理程式。 在某些情況下，有多個服務參與程序，因此它們可以是罪魁禍首。 

## <a name="understanding-the-alert"></a>了解警示

**警示詳細資料**刀鋒視窗會顯示當警示發生，以及上次偵測。 背景處理序執行每隔兩小時產生，並重新評估警示。 在下列範例中，初始的警示發生 03/10 日上午 9:59。 警示仍存在於上 03/12 日上午 10:00 警示時重新評估。 [] 刀鋒視窗也詳細說明健全狀況服務前次收到特定的資料類型的時間。 
 
 ![Azure AD Connect Health 警示詳細資料](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
下表會將服務類型對應至相對應的所需的資料類型：

| 服務類型 | 代理程式 （Windows 服務名稱） | 目的 | 產生的資料類型  |
| --- | --- | --- | --- |  
| Azure AD Connect （同步） | Azure AD Connect Health Sync Insights 服務 | 收集 AAD Connect 特有的資訊 （連接器、 同步處理規則） | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring 服務 | AAD Connect 特定效能計數器、 ETW 追蹤、 檔案收集 | 效能計數器 |
| AD DS | Azure AD Connect Health AD DS Insights 服務 | 執行綜合測試，收集拓撲資訊，複寫中繼資料 |  - Adds-TopologyInfo-Json <br /> -一般-TestData-Json （建立測試結果）   | 
|  | Azure AD Connect Health AD DS 監視服務 | 收集加入特定效能計數器、 ETW 追蹤檔案 | -效能計數器  <br /> -一般-TestData-Json （上傳測試結果）  |
| AD FS | Azure AD Connect Health AD FS 診斷服務 | 執行綜合測試 | TestResult （建立測試結果） | 
| | Azure AD Connect Health AD FS Insights 服務  | 收集 ADFS 使用計量 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS 監視服務 | 收集 ADFS 特定效能計數器、 ETW 追蹤檔案 | TestResult （上傳測試結果） |

## <a name="troubleshooting-steps"></a>疑難排解步驟 

若要診斷問題所需的步驟如下所示。 首先是一組通用於所有服務類型的基本檢查。 下表列出特定的步驟，每個服務類型和資料類型。 

> [!IMPORTANT] 
> 此警示會遵循 Connect Health 的[資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)

* 請確定已安裝最新版的代理程式。 檢視[發行大事記](reference-connect-health-version-history.md)。 
* 請確定 Azure AD Connect Health 代理程式服務都**執行**機器上。 例如，適用於 AD FS 的 Connect Health 應該要有三個服務。
  ![驗證 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 確實詳讀[需求小節](how-to-connect-health-agent-install.md#requirements)，並符合相關要求。
* 使用[測試連線工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)找出連線問題。
* 如果您有 HTTP Proxy，請遵循這些[設定步驟](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。 


## <a name="next-steps"></a>後續步驟
如果上述步驟中的任何識別發生問題時，修正此問題，並等候要解決的警示。 警示的背景處理序會執行每隔 2 小時，因此將會花費最多 2 小時才會解決警示。 

* [Azure AD Connect Health 資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health 常見問題集](reference-connect-health-faq.md)
