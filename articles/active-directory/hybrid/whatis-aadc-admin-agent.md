---
title: 什麼是 Azure AD Connect 管理代理程式-Azure AD Connect |Microsoft Docs
description: 描述用來搭配 Azure AD 進行同步處理及監視您內部部署環境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305207"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>什麼是 Azure AD Connect 管理代理程式？ 
Azure AD Connect 管理代理程式是 Azure Active Directory Connect 的新元件，可以安裝在 Azure Active Directory Connect 伺服器上。 它是用來從您的 Active Directory 環境收集特定資料，協助 Microsoft 支援工程師在您開啟支援案例時，針對問題進行疑難排解。 

>[!NOTE]
>系統管理員代理程式預設不會安裝和啟用。  您必須安裝代理程式，才能收集資料以協助支援案例。

安裝時，Azure AD Connect 管理代理程式會等候來自 Azure Active Directory 的資料的特定要求、從同步環境取得要求的資料，並將其傳送至 Azure Active Directory，並在其中呈現給 Microsoft 支援服務引擎. 

Azure AD Connect 系統管理代理程式從您的環境中抓取的資訊並不是以任何方式儲存，只會向 Microsoft 支援工程師顯示，以協助他們調查和疑難排解 Azure Active Directory Connect預設不會在 Azure AD Connect 伺服器上安裝您開啟 Azure AD Connect 系統管理代理程式的相關支援案例。 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>在 Azure AD Connect 伺服器上安裝 Azure AD Connect 系統管理代理程式 

必要條件：
1.  伺服器上已安裝 Azure AD Connect
2.  伺服器上已安裝 Azure AD Connect Health

![管理代理程式](media/whatis-aadc-admin-agent/adminagent0.png)

Azure AD Connect 系統管理代理程式二進位檔會放在 AAD Connect 伺服器中。 若要安裝代理程式，請執行下列動作：

1.  在系統管理員模式中開啟 powershell
2.  流覽至應用程式所在的目錄 cd "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.  執行 ConfigureAdminAgent

出現提示時，請輸入您的 Azure AD 全域管理員認證。 這應該是 Azure AD Connect 安裝期間輸入的相同認證。

安裝代理程式之後，您會在伺服器的 [控制台] 中的 [新增/移除程式] 清單中看到下列兩個新程式： 

![管理代理程式](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>我的同步處理服務中會向 Microsoft 服務工程師顯示哪些資料？ 
當您開啟支援案例時，Microsoft 支援服務工程師可以查看指定使用者的相關資料、Active Directory 中的相關資料、Azure Active Directory Connect 伺服器中的 Active Directory 連接器空間、Azure 中的 Azure Active Directory 連接器空間Active Directory 連接伺服器和 Azure Active Directory Connect 伺服器中的元節。 

Microsoft 支援服務工程師無法變更系統中的任何資料，而且看不到任何密碼。 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>如果我不想讓 Microsoft 支援工程師存取我的資料，該怎麼辦？ 
安裝代理程式後，如果您不想讓 Microsoft 服務工程師存取您的資料以進行支援呼叫，您可以修改服務設定檔案來停用此功能，如下所述： 

1.  在 [記事本] 中開啟**C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** 。
2.  停用**UserDataEnabled**設定，如下所示。 如果**UserDataEnabled**設定存在，而且設定為 true，則將它設定為 false。 如果設定不存在，則新增設定，如下所示。    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  儲存組態檔。
4.  重新開機 Azure AD Connect 管理代理程式服務，如下所示

![管理代理程式](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
