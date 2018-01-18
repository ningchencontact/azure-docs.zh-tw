---
title: "在 Azure Active Directory 中使用 Cloud App Discovery 尋找非受控雲端應用程式 | Microsoft Docs"
description: "提供有關使用 Cloud App Discovery 尋找和管理應用程式、它的優勢和運作方式的資訊。"
services: active-directory
keywords: "雲端應用程式探索, 管理應用程式"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>使用 Cloud App Discovery 尋找非受控雲端應用程式
## <a name="summary"></a>總結

Azure Active Directory 中的 Cloud App Discovery 現在提供 Microsoft Cloud App Security 所授權的增強式無代理程式探索體驗。 若要使用 Cloud App Discovery，只需要使用 Azure AD Premium P1 認證進行登入。 所有 Azure AD Premium P1 客戶都可以免費使用此更新。 開始使用[在 Azure AD 中設定 Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started) 一文，然後試用 [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/)。

> [!IMPORTANT] 
> 2018 年 3 月 5 日將會關閉具有以代理程式為基礎之探索的目前 Azure AD Cloud App Discovery 體驗，在此日期之後，會停用代理程式並刪除資料。 請在 3 月 5 日之前採取動作，以開始使用新的體驗，避免服務中斷。  
 
**使用 Cloud App Discovery，您可以：**

* 尋找使用的雲端應用程式，並依使用者數目、流量或應用程式的 Web 要求數目測量使用量。
* 識別正在使用應用程式的使用者。
* 匯出資料以進行離線分析。
* 讓這些應用程式在 IT 的控制下並為使用者管理啟用單一登入。

## <a name="how-it-works"></a>運作方式
1. 應用程式使用代理程式會安裝在使用者的電腦上。
2. 代理程式所擷取的應用程式使用情況資訊會透過安全的加密通道，傳送到 Cloud App Discovery 服務。
3. Cloud App Discovery 服務會評估資料並產生報告。

![Cloud App Discovery 圖表](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>後續步驟
* [Cloud App Discovery 的安全性和隱私權考量](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [具有自訂連接埠的 Proxy 伺服器的 Cloud App Discovery 登錄設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery 代理程式變更記錄 ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

