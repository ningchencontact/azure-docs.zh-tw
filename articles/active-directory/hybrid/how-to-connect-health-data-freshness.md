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
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803284"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>健康情況服務的資料並非最新警示

## <a name="overview"></a>概觀
位於 Azure Active Directory Connect Health 監視的內部部署電腦上的代理程式，會定期將資料上傳到 Azure AD Connect Health 服務。 如果該服務沒有從代理程式接收到資料，入口網站中所顯示的資訊將會過時。 為了醒目提示該問題，服務將會引發 [健康情況服務資料不是最新的資料] 警示。 它就會產生服務過去兩個小時內未收到完成的資料。  

* **警告**狀態警示引發時，如果此健全狀況服務已收到只**部分**過去兩個小時內從伺服器傳送的資料類型。 警告狀態警示不會觸發設定的收件者的電子郵件通知。 
* **錯誤**如果健全狀況服務未收到任何資料類型的伺服器在過去 2 小時，就會引發狀態警示。 觸發警示的錯誤狀態的電子郵件通知給已設定的收件者。

服務取得的資料從內部部署機器執行的代理程式。 根據服務類型下, 表列出在執行的動作以及服務所產生的資料類型的機器執行的代理程式。 在某些情況下，有多個程序，因此其中一個相關的服務可能是問題。 

## <a name="understanding-the-alert"></a>了解警示
[警示詳細資料] 刀鋒視窗會指出當引發警示並上次偵測的時間。 警示為產生/重新-evaluated 背景處理序執行每隔兩小時。 在下列範例中，初始引發警示的 03/10 日上午 9:59。 它一直存在，即使在 03/12 上午 10:00 警示時重新評估。
[] 刀鋒視窗也會詳述上次健全狀況服務收到特定的資料類型的時間。 
 
 ![Azure AD Connect Health 警示詳細資料](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
以下是服務類型和對應的所需的資料類型的對應。

| 服務類型 | 代理程式 （Windows 服務名稱） | 目的 | 產生的資料類型  |
| --- | --- | --- | --- |  
| Azure AD Connect （同步） | Azure AD Connect Health Sync Insights 服務 | 收集 AAD Connect 的特定資訊 （連接器，同步處理規則等。） | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring 服務 | 收集 (AAD Connect 特定) 效能計數器，ETW 追蹤檔案 | 效能計數器 |
| AD DS | Azure AD Connect Health AD DS Insights 服務 | 執行綜合測試，收集拓撲資訊，複寫中繼資料 |  - Adds-TopologyInfo-Json <br /> -一般-TestData-Json （建立測試結果）   | 
|  | Azure AD Connect Health AD DS 監視服務 | 收集 （特定的 ADDS） 效能計數器，ETW 追蹤檔案 | -效能計數器  <br /> -一般-TestData-Json （上傳測試結果）  |
| AD FS | Azure AD Connect Health AD FS 診斷服務 | 執行綜合測試 | TestResult （建立測試結果） | 
| | Azure AD Connect Health AD FS Insights 服務  | 收集 ADFS 使用計量 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS 監視服務 | 收集 （特定的 ADFS） 效能計數器，ETW 追蹤檔案 | TestResult （上傳測試結果） |

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
