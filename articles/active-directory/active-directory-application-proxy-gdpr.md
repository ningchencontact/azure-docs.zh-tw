---
title: Azure Active Directory 應用程式 Proxy 中的 GDPR | Microsoft Docs
description: 深入了解 Azure Active Directory 應用程式 Proxy 中 GDPR。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory 應用程式 Proxy 中的 GDPR  

Azure Active Directory (Azure AD) 應用程式 Proxy 以及所有其他 Microsoft 服務與功能均符合 GDPR。 若要深入了解 Microsoft 的 GDPR 支援，請參閱[授權條款及文件](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)。

因為這項功能包括您電腦上的連接器，您需要監視幾個事件才能持續符合 GDPR。 應用程式 Proxy 會建立下列可能包含 EUII 的記錄類型：

- 連接器事件記錄檔
- Windows 事件記錄檔

有兩種方式可讓您保持符合 GDPR 規範：

- 刪除及匯出所出現的要求

- 關閉記錄

關於：

- 有關如何設定 Windows 事件記錄之資料保留期的資訊，請參閱[事件記錄的設定](https://technet.microsoft.com/library/cc952132.aspx)。 
- 有關 Windows 事件記錄的一般資訊，請參閱[使用 Windows 事件記錄](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)。


您可以在 `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` 找到連線事件記錄。 下列章節會向您提供連接器事件記錄的相關步驟。 您必須在所有連接器電腦上完成這些步驟。
 

## <a name="processing-requests"></a>處理要求

您需要負責處理的要求有三種不同類型： 

- 刪除
- 檢視
- 匯出
 
若要處理檢視/匯出要求，您需要查看每個記錄檔來搜尋相關項目。 

由於記錄為文字檔，您可以藉由 (舉例來說) 使用 `findstr` 命令進行搜尋，以尋找與使用者相關的項目。 搜尋下列欄位，因為它們可能會在記錄中： 

- UserId
- 為任何使用 Kerberos 限制委派之應用程式所設定的使用者名稱類型：
    - 使用者主體名稱
    - 內部部署使用者主體名稱
    - 使用者主體名稱的使用者名稱部分
    - 內部部署使用者主體名稱的使用者名稱部分
    - 內部部署 SAM 帳戶名稱 

 
您接著可以收集這些欄位，並與使用者分享。
若要處理刪除要求，您需要刪除相關的記錄。 您可以重新啟動連接器服務 (Microsoft Azure AD 應用程式 Proxy 連接器) 來產生新的記錄檔。 新的記錄檔可讓您刪除舊的記錄檔。 您接著可以遵循檢視/匯出的程序來尋找所有相關記錄，並選擇性地刪除那些欄位或檔案。 如果您不再需要舊的記錄檔，您一律也可以直接加以刪除。


## <a name="turn-off-connector-logs"></a>關閉連接器記錄

若要關閉連接器記錄，您需要調整 `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`，方法是移除醒目提示的文字行： 


![組態](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>後續步驟

如需 Azure AD 應用程式 proxy 的概觀，請參閱[如何為內部部署應用程式提供安全的遠端存取](manage-apps/application-proxy.md)。

