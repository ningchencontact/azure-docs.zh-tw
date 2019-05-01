---
title: 什么是 Azure AD Connect 管理代理 - Azure AD Connect | Microsoft Docs
description: 描述用來搭配 Azure AD 進行同步處理及監視您內部部署環境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687236"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>什麼是 Azure AD Connect 管理代理程式？ 
Azure AD Connect 管理代理程式是 Azure Active Directory Connect 可在 Azure Active Directory Connect 的伺服器上安裝的新元件。 该组件用于从 Active Directory 环境收集特定的数据，以帮助 Microsoft 支持工程师在收到支持案例时排查问题。 

>[!NOTE]
>尚未安裝及預設啟用的管理代理程式。  您必須安裝代理程式，才能收集資料，以協助的支援案例。

安裝時，針對特定要求，Azure AD Connect 管理代理程式等候的資料從 Azure Active Directory 中，從同步處理環境中取得要求的資料，並將它傳送至 Azure Active Directory，其中就會向 Microsoft 支援工程師。 

Azure AD Connect 管理代理程式會擷取您的環境的資訊不會儲存在任何方式-它只會顯示給 Microsoft 支援工程師，以協助他們在調查及疑難排解 Azure Active Directory ConnectAzure AD Connect 伺服器上預設未安裝相關的支援案例，您會開啟 Azure AD Connect 管理代理程式。 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Azure AD Connect 伺服器上安裝 Azure AD Connect 管理代理程式 
Azure AD Connect 管理代理程式二進位檔會放置在 AAD Connect 伺服器。 若要安裝代理程式，執行下列作業： 



1. 在管理員模式開啟 powershell 
2. 瀏覽至應用程式與位於的 cd"C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles"所在的目錄 
3. 執行 AADConnectAdminAgentSetup.exe 應用程式 
 
出現提示時，請輸入您的 Azure AD 全域管理員認證。 

>[!NOTE]
>沒有已知的問題，您將會提示您輸入認證多次。 這將在下一版中修正。

安裝代理程式之後，您會看到您的伺服器控制項台中的 [新增/移除程式] 清單中的下列兩個新程式： 

![管理代理](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>向 Microsoft 服务工程师显示同步服务中的哪些数据？ 
當您開啟支援案例，Microsoft 支援工程師所見，指定的使用者在 Active Directory 中的相關資料、 Active Directory 連接器空間中的 Azure Active Directory Connect 的伺服器，在 Azure 中的 Azure Active Directory 連接器空間Active Directory Connect 的伺服器與 Metaverse 中的 Azure Active Directory Connect 的伺服器。 

Microsoft 支持工程师无法更改你的系统中的任何数据，且无法查看任何密码。 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>如果我不希望 Microsoft 支持工程师访问我的数据，该怎样做？ 
代理程式安裝之後，如果您不想要存取您的資料，支援呼叫 Microsoft 服務工程師，您可以修改服務組態檔，如下所述來停用的功能： 

1.  開啟**C:\Program Files\Microsoft Azure AD Connect 管理 Agent\AzureADConnectAdministrationAgentService.exe.config**在記事本中。
2.  按如下所示禁用 **UserDataEnabled** 设置。 如果 **UserDataEnabled** 设置存在并已设置为 true，请将其设置为 false。 如果该设置不存在，请按如下所示添加该设置。    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  儲存組態檔。
4.  按如下所示重启 Azure AD Connect 管理代理服务

![管理代理](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
