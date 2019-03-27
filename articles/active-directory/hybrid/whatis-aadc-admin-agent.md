---
title: 什麼是 Azure AD Connect 管理代理程式-Azure AD Connect |Microsoft Docs
description: 描述用來搭配 Azure AD 進行同步處理及監視您內部部署環境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b99b7b03898ad72b4d9c7cc2ba9154855475ef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488219"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>什麼是 Azure AD Connect 管理代理程式？ 
Azure AD Connect 管理代理程式是 Azure Active Directory Connect 的 Azure Active Directory Connect 的伺服器上所安裝的新元件。 它用來從 Active Directory 環境，可協助 Microsoft 支援工程師以疑難排解問題，當您開啟支援案例時收集特定資料。

安裝時，針對特定要求，Azure AD Connect 管理代理程式等候的資料從 Azure Active Directory 中，從同步處理環境中，取得要求的資料，並將它傳送至 Azure Active Directory，其中就會向 Microsoft 支援工程師。

Azure AD Connect 管理代理程式會擷取您的環境的資訊不會儲存在任何方式-它只會顯示給 Microsoft 支援工程師，以協助他們在調查及疑難排解 Azure Active Directory Connect開啟相關的支援案例

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Azure AD Connect 伺服器安裝 Azure AD Connect 管理代理程式的方式？ 
安裝管理代理程式之後，您會看到您的伺服器控制項台中的 [新增/移除程式] 清單中的下列兩個新程式： 

![管理代理程式](media/whatis-aadc-admin-agent/adminagent1.png)

請勿移除或解除安裝程式，因為它們是將 Azure AD Connect 安裝的重要部分。

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Microsoft 服務工程師會顯示在 我的同步處理服務中的哪些資料？
當您開啟支援案例，Microsoft 支援工程師所見，指定的使用者在 Active Directory 中的相關資料、 Active Directory 連接器空間中的 Azure Active Directory Connect 的伺服器，在 Azure 中的 Azure Active Directory 連接器空間Active Directory Connect 的伺服器與 Metaverse 中的 Azure Active Directory Connect 的伺服器。

Microsoft 支援工程師無法看到任何密碼，而且無法變更系統中的任何資料。

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>如果我不想 Microsoft 支援工程師，若要存取我的資料嗎？ 
 
如果您不想 Microsoft 服務工程師，以存取您的資料，支援呼叫，當您開啟支援表示這會呼叫在入口網站： 

  1.    開啟**C:\Program Files\Microsoft Azure AD Connect 管理 Agent\AzureADConnectAdministrationAgentService.exe.config**在記事本中。
  2.    停用**UserDataEnabled**設定，如下所示。 如果**UserDataEnabled**設定存在，而且設定為 true，則將它設定為 false。 如果設定不存在，然後將設定，如下所示。    
  `
 <appSettings>
   <add key="TraceFilename" value="ADAdministrationAgent.log" />
   <add key="UserDataEnabled" value="false" />
  </appSettings>
  `
  3.    儲存組態檔。
  4.    重新啟動 Azure AD Connect 管理代理程式服務，如下所示

![管理代理程式](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。